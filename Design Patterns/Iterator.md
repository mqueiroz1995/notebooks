:warning: This notebook is heavily based on the book "Design Patterns: Elements of Reusable Object-Oriented Software" by Erich Gamma, John Vlissides, Ralph Johnson, and Richard Helm

# Iterator <!-- omit in toc -->

Also known as *Cursor*.

## Table of Contents <!-- omit in toc -->
- [Intent](#intent)
- [Applicability](#applicability)
- [Implementation](#implementation)
- [Trade-Offs](#trade-offs)
- [References](#references)

## Intent

Provide a way to access the elements of an aggregate object sequentially without exposing its underlying representation.

## Applicability

Use the Iterator pattern:
* to access an aggregate object's contents without exposing its internal representation.
* to support multiple traversals of aggregate objects.
* to provide a uniform interface for traversing different aggregate structures (that is, to support polymorphic iteration).

## Implementation

```java
package design_patters;

//We could have used Java.Util.Iterator
interface Iterator<T> {
	boolean hasNext();

	T next();
}

interface Aggregate<T> {
	public Iterator<T> createIterator();
}

class Notification {
	private String title;
	private String message;

	public Notification(String title, String message) {
		this.title = title;
		this.message = message;
	}

	public String getTitle() {
		return title;
	}

	public String getMessage() {
		return message;
	}
}

class NotificationAggregate implements Aggregate<Notification> {
	private static final int MAX_ITEMS = 6;
	private int numberOfItems = 0;
	private Notification[] notificationList;

	public NotificationAggregate() {
		notificationList = new Notification[MAX_ITEMS];
	}

	public NotificationIterator createIterator() {
		return new NotificationIterator(this);
	}

	public void addItem(String title, String message) {
		Notification notification = new Notification(title, message);
		if (numberOfItems >= MAX_ITEMS) {
			System.err.println("NotificationCollection is full.");
		} else {
			notificationList[numberOfItems] = notification;
			numberOfItems = numberOfItems + 1;
		}
	}

	public int getSize() {
		return numberOfItems;
	}

	public Notification get(int position) {
		if (position >= 0 && position < getSize()) {
			return notificationList[position];
		} else {
			throw new ArrayIndexOutOfBoundsException();
		}
	}
}

class NotificationIterator implements Iterator<Notification> {
	private NotificationAggregate notifications;
	private int currentPosition = 0;

	public NotificationIterator(NotificationAggregate notifications) {
		this.notifications = notifications;
	}

	public Notification next() {
		Notification notification = notifications.get(currentPosition);
		currentPosition += 1;
		return notification;
	}

	public boolean hasNext() {
		if (currentPosition >= notifications.getSize()) {
			return false;
		} else {
			return true;
		}
	}
}

class BackwardNotificationIterator implements Iterator<Notification> {
	private NotificationAggregate notifications;
	private int currentPosition;

	public BackwardNotificationIterator(NotificationAggregate notifications) {
		this.notifications = notifications;
		this.currentPosition = notifications.getSize() - 1;
	}

	public Notification next() {
		Notification notification = notifications.get(currentPosition);
		currentPosition -= 1;
		return notification;
	}

	public boolean hasNext() {
		if (currentPosition < 0) {
			return false;
		} else {
			return true;
		}
	}
}

class Main {
	public static void main(String args[]) {
		NotificationAggregate notifications = new NotificationAggregate();
		notifications.addItem("Title 1", "Message 1");
		notifications.addItem("Title 2", "Message 2");
		notifications.addItem("Title 3", "Message 3");

		printNotifications(notifications);
		printNotificationsBackward(notifications);
	}

	private static void printNotifications(NotificationAggregate notifications) {
		NotificationIterator iterator = new NotificationIterator(notifications);
		while (iterator.hasNext()) {
			Notification notification = iterator.next();
			System.out.println(notification.getTitle() + " - " + notification.getMessage());
		}
	}

	private static void printNotificationsBackward(NotificationAggregate notifications) {
		BackwardNotificationIterator iterator = new BackwardNotificationIterator(notifications);
		while (iterator.hasNext()) {
			Notification notification = iterator.next();
			System.out.println(notification.getTitle() + " - " + notification.getMessage());
		}
	}
}
```

## Trade-Offs

1. *Who controls the iteration?* A fundamental issue is deciding which party controls the iteration, the iterator or the client that uses the iterator. When the client controls the iteration, the iterator is called an **external iterator**, and when the iterator controls it, the iterator is an **internal iterator**. Clients that use an external iterator must advance the traversal and request the next element explicitly from the iterator. In contrast, the client hands an internal iterator an operation to perform, and the iterator applies that operation to every element in the aggregate.<br>
External iterators are more flexible than internal iterators. It's easy to compare two collections for equality with an external iterator, for example, but it's practically impossible with internal iterators. Internal iterators are especially weak in a language like C++ that does not provide anonymous functions, closures, or continuations like Smalltalk and CLOS. But on the other hand, internal iterators are easier to use, because they define the iteration logic for you.

2. *Who defines the traversal algorithm?* The iterator is not the only place where the traversal algorithm can be defined. The aggregate might define the traversal algorithm and use the iterator to store just the state of the iteration. We call this kind of iterator a **cursor**, since it merely points to the current position in the aggregate. A client will invoke the Next operation on the aggregate with the cursor as an argument, and the Next operation will change the state of the cursor.<br>
If the iterator is responsible for the traversal algorithm, then it's easy to use different iteration algorithms on the same aggregate, and it can also be easier to reuse the same algorithm on different aggregates. On the other hand, the traversal algorithm might need to access the private variables of the aggregate. If so, putting the traversal algorithm in the iterator violates the encapsulation of the aggregate.

3. *How robust is the iterator?* It can be dangerous to modify an aggregate while you're traversing it. If elements are added or deleted from the aggregate, you might end up accessing an element twice or missing it completely. A simple solution is to copy the aggregate and traverse the copy, but that's too expensive to do in general.<br>
A **robust iterator** ensures that insertions and removals won't interfere with traversal, and it does it without copying the aggregate. There are many ways to implement robust iterators. Most rely on registering the iterator with the aggregate. On insertion or removal, the aggregate either adjusts the internal state of iterators it has produced, or it maintains information internally to ensure proper traversal.<br>

4. *Additional Iterator operations*. The minimal interface to Iterator consists of the operations First, Next, IsDone, and CurrentItem. Some additional operations might prove useful. For example, ordered aggregates can have a Previous operation that positions the iterator to the previous element. A SkipTo operation is useful for sorted or indexed collections. SkipTo positions the iterator to an object matching specific criteria.

5. *Using polymorphic iterators in C++.* Polymorphic iterators have their cost. They require the iterator object to be allocated dynamically by a factory method. Hence they should be used only when there's a need for polymorphism. Otherwise use concrete iterators, which can be allocated on the stack.<br>
Polymorphic iterators have another drawback: the client is responsible for deleting them. This is error-prone, because it's easy to forget to free a heap-allocated iterator object when you're finished with it. That's especially likely when there are multiple exit points in an operation. And if an exception is triggered, the iterator object will never be freed.<br>
The Proxy pattern provides a remedy. We can use a stack-allocated proxy as a stand-in for the real iterator. The proxy deletes the iterator in its destructor. Thus when the proxy goes out of scope, the real iterator will get deallocated along with it. The proxy ensures proper cleanup, even in the face of exceptions. This is an application of the well-known C++ technique "resource allocation is initialization".

6. *Iterators may have privileged access*. An iterator can be viewed as an extension of the aggregate that created it. The iterator and the aggregate are tightly coupled. We can express this close relationship in C++ by making the iterator a friend of its aggregate. Then you don't need to define aggregate operations whose sole purpose is to let iterators implement traversal efficiently.<br>
However, such privileged access can make defining new traversals difficult, since it'll require changing the aggregate interface to add another friend. To avoid this problem, the Iterator class can include protected operations for accessing important but publicly unavailable members of the aggregate. Iterator subclasses (and only Iterator subclasses) may use these protected operations to gain privileged access to the aggregate.

7. *Iterators for composites*. External iterators can be difficult to implement over recursive aggregate structures like those in the Composite pattern, because a position in the structure may span many levels of nested aggregates. Therefore an external iterator has to store a path through the Composite to keep track of the current object. Sometimes it's easier just to use an internal iterator. It can record the current position simply by calling itself recursively, thereby storing the path implicitly in the call stack.<br>
If the nodes in a Composite have an interface for moving from a node to its siblings, parents, and children, then a cursor-based iterator may offer a better alternative. The cursor only needs to keep track of the current node; it can rely on the node interface to traverse the Composite.
Composites often need to be traversed in more than one way. Preorder, postorder, inorder, and breadth-first traversals are common. You can support each kind of traversal with a different class of iterator.

8. *Null iterators*. A **NullIterator** is a degenerate iterator that's helpful for handling boundary conditions. By definition, a NullIterator is always done with traversal; that is, its IsDone operation always evaluates to true.<br>
NullIterator can make traversing tree-structured aggregates (like Composites) easier. At each point in the traversal, we ask the current element for an iterator for its children. Aggregate elements return a concrete iterator as usual. But leaf elements return an instance of NullIterator. That lets us implement traversal over the entire structure in a uniform way.

## References

* Design Patterns: Elements of Reusable Object-Oriented Software by Erich Gamma, John Vlissides, Ralph Johnson, and Richard Helm
* https://sourcemaking.com/design_patterns/iterator/
* https://www.geeksforgeeks.org/iterator-pattern/
