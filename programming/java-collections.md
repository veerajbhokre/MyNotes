Set:

- HashSet
    - no duplicate
    - No order
- LinkedHashSet
    - No duplicate
    - Order
- TreeSet
    - place number in sorted order
    - Important methods,
        - decendingSet() -> reverse sort order
        - headSet(10) -> gives lower than 10
        - tailSet(10) -> higher than 10
        - subSet(4, 11) -> gives between 4 and 11
        - lower (10) ->  imediate lower than 10
        - higher(10) -> imediate higher than 10

List:

- ArrayList
- LinkedList

Queue:

- Important methods:
    - add, remove, element -> adds , remove and get element from queue (throws exception if cannot be added ot removed
      from empty)
    - offer, poll, peek -> Same as above bit dont throw exception but return boolean or null.

- ArrayDeque
    - Also can be sued as stack,
    - Have methods like push, pop.
- PriorityQueue
    - Allows comparator to be provided to determine the order of the queue.

Comparator and Comparable:

```java
public class Player {
    private int ranking;
    private String name;
    private int age;

    // constructor, getters, setters  
}

public class Main {
    public static void main(String[] args) {
        List<Player> footballTeam = new ArrayList<>();
        Player player1 = new Player(59, "John", 20);
        Player player2 = new Player(67, "Roger", 22);
        Player player3 = new Player(45, "Steven", 24);
        footballTeam.add(player1);
        footballTeam.add(player2);
        footballTeam.add(player3);

        System.out.println("Before Sorting : " + footballTeam);
        Collections.sort(footballTeam);
        System.out.println("After Sorting : " + footballTeam);

        // COMPARABLE

        // The method sort(List<T>) in the type Collections is not applicable for the arguments (ArrayList<Player>)

        // Need the class to be comparable like given below.
        /**   public class Player implements Comparable<Player> {
         // same as before
         @Override public int compareTo(Player otherPlayer) {
         return Integer.compare(getRanking(), otherPlayer.getRanking());
         }
         } **/

        // The sorting order is decided by the return value of the compareTo() method.
        // The Integer.compare(x, y) returns -1 if x is less than y, 0 if they’re equal, and 1 otherwise.

        //COMPARATOR

        //The Comparator interface defines a compare(arg1, arg2) method 

        Comparator byRanking =
                (Player player1, Player player2) -> Integer.compare(player1.getRanking(), player2.getRanking());

        // OR

        Comparator<Player> byRanking = Comparator
                .comparing(Player::getRanking);
        Comparator<Player> byAge = Comparator
                .comparing(Player::getAge);

        Collections.sort(footballTeam, byRanking);
    }
}
```
