Add Logic: Find the window and lock it. Validate the window and insert the new node. Release the lock.

Remove Logic: Find the window and lock it. Validate the window and remove the node. Release the lock.

Replace Logic:
First add new node if not exists and remove odd node if exists. Before adding the node into list, set a reference to the node to be reomved if exists. Then add the new node to the list.

Strategy to test:

Use ThreadLocal class in java for each thread to record the number of change  of add/remove/replace. Sum these number for each thread to get expected length of list. Compare actual length of list with the expected length of the list.

I use 10 threads that choose add/remove/replace action randomly with number 1-20, and run each thread 100000,1000000,10000000 times. They all correct actual length of list as expected.


Pseudo-code for replace:

```java
public int replace_return_int(T oldItem, T newItem) {
//In order to test, return value has been changed to 
//the number of length change of the list modified by replace
		int newkey = newItem.hashCode();
		int oldkey = oldItem.hashCode();
		if (newkey == oldkey) {
			return 0;
		}
		while (true) {
			Find window for add
			Find window for delete
			Lock both window(4 nodes) according to the key value 
    		if (both window is valid) 
    		{
    			if (there is a new key and there isn`t an oldkey) {
    				return 0;
    			} else if (there isn`t a new key and there isn`t an oldkey) {
    			    //just add
    				add new node
    				return 1;
    			} else if (there is a new key and there is an oldkey) {
    			    //just remove
    				remove the old node
    				return -1;
    			} else { // if there isn`t new node and there is an old node add then remove
			        //add
    				create a new node with reference to the node to be removed
    				add this node into list
    				//remove
    				delcurr.marked = true; // mark
    				if (two windows are the same) {
    					remove the node by connect it`s next with new node
    				} else {
    					normal remove the node
    				}
    				return 0;
    			}              
    		}
    		Release the lock according to key value;
		}
	}
	
```
Actually Code:
```java
public int replace_return_int(T oldItem, T newItem) {
		// in order to test I set return value to the change of the list
		int newkey = newItem.hashCode();
		int oldkey = oldItem.hashCode();
		if (newkey == oldkey) {
			return 0;
		}
		//find window
		while (true) {
			Node<T> addpred = this.head;
			Node<T> addcurr = head.next;
			while (addcurr.key < newkey) {
				addpred = addcurr;
				addcurr = addcurr.next;
			}
			Node<T> delpred = this.head;
			Node<T> delcurr = head.next;
			while (delcurr.key < oldkey) {
				delpred = delcurr;
				delcurr = delcurr.next;
			}
			Node[] s = this.sort(addpred, addcurr, delpred, delcurr);
			//lock according to key value of each node
			s[0].lock.lock();
			try {
				s[1].lock.lock();
				try {
					s[2].lock.lock();
					try {
						s[3].lock.lock();
						try {

		if (validate(addpred, addcurr) && validate(delpred, delcurr)) {
			if (addcurr.key == newkey && delcurr.key != oldkey) {
				// if there is a new key and there isn't an oldkey do nothing
				return 0;
			} else if (addcurr.key != newkey && delcurr.key != oldkey) {
				// just add
				Node<T> node = new Node<T>(newItem);
				node.pair = null;
				node.next = addcurr;
				addpred.next = node;
				return 1;
			} else if (addcurr.key == newkey && delcurr.key == oldkey) {
				// just remove
				delcurr.marked = true; // mark
				delpred.next = delcurr.next; // physically remove
				return -1;
			} else { // if there isn't new node and there is an old node add then remove
				// add
				Node<T> node = new Node<T>(newItem);
				node.pair = delcurr;// it should be set before it insert into list
				node.next = addcurr;
				addpred.next = node;
				// remove
				delcurr.marked = true; // mark
				if (delcurr.key == addcurr.key) {
					node.next = delcurr.next;
					// physically remove in condition window is the
												// same
					node.pair = null;
				} else {
					delpred.next = delcurr.next; // physically remove
					node.pair = null;
				}
				return 0;
			}
		}
	    } // vali
						finally {
							s[3].lock.unlock();
						}
					} finally {
						s[2].lock.unlock();
					}
				} finally {
					s[1].lock.unlock();
				}
			} finally {
				s[0].lock.unlock();
			}

		}
	}
	public boolean validate(Node<T> pred, Node<T> curr) {
	// to make sure the node is not marked or from a incomplete replace action
		return !pred.marked && !curr.marked && pred.next == curr;
 //no need to judge reference in add of replace because 
 //if other thread can lock new added node and the next node,
 //the replace has been finished
	}
	
	public boolean add(T item) {
		// 1.find the window
		int key = item.hashCode();
		while (true) {
			Node<T> pred = head;
			Node<T> curr = head.next;
			while (curr.key < key) {
				pred = curr;
				curr = curr.next;
			}
			// 2.lock the window
			pred.lock.lock();
			try {
				curr.lock.lock();
				try {
					// 3.validate the window
					if (validate(pred, curr)) {
						if (curr.key == key) {
							return false;
						} else {
							// 4.add the new node
							Node<T> node = new Node<T>(item);
							node.next = curr;
							pred.next = node;
							return true;
						}
					}
				} finally {
					curr.lock.unlock();
				}
			} finally {
				pred.lock.unlock();
			}
		}
	}

	public boolean remove(T item) {
		// 1.find the windows
		int key = item.hashCode();
		while (true) {
			Node<T> pred = this.head;
			Node<T> curr = head.next;
			while (curr.key < key) {
				pred = curr;
				curr = curr.next;
			}
			// 2.lock the window
			pred.lock.lock();
			try {
				curr.lock.lock();
				try {
					// 3.validate the window
					if (validate(pred, curr)) {
						if (curr.key != key) {
							return false;
						} else {
							// 4.mark and remove the node
							curr.marked = true; // mark to ture
							pred.next = curr.next; // actually removed
							return true;
						}
					}
				} finally { // always unlock curr
					curr.lock.unlock();
				}
			} finally { // always unlock pred
				pred.lock.unlock();
			}
		}
	}
```