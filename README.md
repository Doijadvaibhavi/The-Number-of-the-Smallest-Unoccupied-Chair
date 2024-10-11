# The-Number-of-the-Smallest-Unoccupied-Chair

There is a party where n friends numbered from 0 to n - 1 are attending. There is an infinite number of chairs in this party that are numbered from 0 to infinity. When a friend arrives at the party, they sit on the unoccupied chair with the smallest number.

For example, if chairs 0, 1, and 5 are occupied when a friend comes, they will sit on chair number 2.
When a friend leaves the party, their chair becomes unoccupied at the moment they leave. If another friend arrives at that same moment, they can sit in that chair.

You are given a 0-indexed 2D integer array times where times[i] = [arrivali, leavingi], indicating the arrival and leaving times of the ith friend respectively, and an integer targetFriend. All arrival times are distinct.

Return the chair number that the friend numbered targetFriend will sit on.

 

Example 1:

Input: times = [[1,4],[2,3],[4,6]], targetFriend = 1
Output: 1
Explanation: 
- Friend 0 arrives at time 1 and sits on chair 0.
- Friend 1 arrives at time 2 and sits on chair 1.
- Friend 1 leaves at time 3 and chair 1 becomes empty.
- Friend 0 leaves at time 4 and chair 0 becomes empty.
- Friend 2 arrives at time 4 and sits on chair 0.
Since friend 1 sat on chair 1, we return 1.
Example 2:

Input: times = [[3,10],[1,5],[2,6]], targetFriend = 0
Output: 2
Explanation: 
- Friend 1 arrives at time 1 and sits on chair 0.
- Friend 2 arrives at time 2 and sits on chair 1.
- Friend 0 arrives at time 3 and sits on chair 2.
- Friend 1 leaves at time 5 and chair 0 becomes empty.
- Friend 2 leaves at time 6 and chair 1 becomes empty.
- Friend 0 leaves at time 10 and chair 2 becomes empty.
Since friend 0 sat on chair 2, we return 2.
 

Constraints:

n == times.length
2 <= n <= 104
times[i].length == 2
1 <= arrivali < leavingi <= 105
0 <= targetFriend <= n - 1
Each arrivali time is distinct.


# SOLUTION

*Intuition

When thinking about the problem, the key is that every friend arriving at the party will sit on the smallest unoccupied chair, and whenever a friend leaves, their chair becomes available again. The challenge is to simulate this process efficiently, keeping track of which chairs are occupied and which are free, especially when multiple friends arrive or leave at the same time. By sorting the events (arrivals and departures) and managing the availability of chairs using a data structure like a min-heap, we can ensure that we always find the smallest available chair.

* Approach
  
Sorting Events: We need to process friends based on their arrival times, so we sort the times array. We also want to efficiently track when friends leave and free their chairs.
Min-Heap for Chairs: The smallest available chair can be easily found using a min-heap. Whenever a friend arrives, we assign them the smallest available chair.
Handling Departures: When a friend leaves, we mark their chair as available again by pushing it back into the min-heap.
Tracking the Target Friend: Once the target friend sits on a chair, we immediately return that chair number.
Complexity
Time Complexity:
Sorting the times array takes (O(n \log n)), where (n) is the number of friends. Each event (either arrival or departure) involves an operation on the min-heap, which takes (O(\log n)). Thus, the overall time complexity is (O(n \log n)).

* Space Complexity:
We use a min-heap to store available chairs, and a priority queue to track when friends leave, resulting in a space complexity of (O(n)).

* JAVA CODE
  
import java.util.*;

class Solution {

    public int smallestChair(int[][] times, int targetFriend) {
    
        int n = times.length;
        
        // List of arrivals with friend index
        
        List<int[]> arrivals = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
        
            arrivals.add(new int[]{times[i][0], i});
            
        }
        
        // Sort friends by arrival time
        
        arrivals.sort((a, b) -> Integer.compare(a[0], b[0]));
        
        // Min-Heap to track available chairs
        
        PriorityQueue<Integer> availableChairs = new PriorityQueue<>();
        
        for (int i = 0; i < n; i++) {
        
            availableChairs.add(i);
            
        }

        // Priority queue to track when chairs are freed
        
        PriorityQueue<int[]> leavingQueue = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        
        // Iterate through each friend based on arrival
        
        for (int[] arrival : arrivals) {
        
            int arrivalTime = arrival[0];
            
            int friendIndex = arrival[1];
            
            // Free chairs that are vacated before the current arrival time
            
            while (!leavingQueue.isEmpty() && leavingQueue.peek()[0] <= arrivalTime) {
            
                availableChairs.add(leavingQueue.poll()[1]);
                
            }
            
            // Assign the smallest available chair
            
            int chair = availableChairs.poll();
            
            
            // If this is the target friend, return their chair number
            
            if (friendIndex == targetFriend) {
            
                return chair;
            }
            
            // Mark the chair as being used until the friend's leave time
            
            leavingQueue.add(new int[]{times[friendIndex][1], chair});
            
        }
        
        return -1; // Should never reach here
        
    }
    
}

* Step-by-Step Detailed Explanation

Sorting Arrivals: First, we sort the friends by their arrival time. This ensures that we process each arrival event in the correct order.
Using Min-Heap for Chairs: We maintain a min-heap of available chairs, where we always pick the smallest chair for the next friend to arrive.
Tracking Departures: We also use a priority queue to track when friends leave, and once they do, their chair becomes available again and is added back to the min-heap.
Finding the Target Friend's Chair: As we process each arrival, if the arriving friend is the target friend, we return the chair assigned to them.
