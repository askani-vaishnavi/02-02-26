import java.util.*;

class Solution {
    public long minimumCost(int[] nums, int k, int dist) {
        int n = nums.length;
        long windowSum = 0;
        // topK stores the smallest k-1 elements in the current window
        TreeMap<Integer, Integer> topK = new TreeMap<>();
        // candidates stores elements in the window that are NOT in the top k-1
        TreeMap<Integer, Integer> candidates = new TreeMap<>();
        int topKCount = 0;

        // Initialize the first window: indices [1, dist + 1]
        for (int i = 1; i <= dist + 1; i++) {
            add(topK, nums[i]);
            windowSum += nums[i];
            topKCount++;
        }

        // Keep only the k-1 smallest elements in topK
        while (topKCount > k - 1) {
            int maxVal = topK.lastKey();
            remove(topK, maxVal);
            add(candidates, maxVal);
            windowSum -= maxVal;
            topKCount--;
        }

        long minCost = windowSum;

        // Slide the window across the rest of the array
        for (int i = dist + 2; i < n; i++) {
            // 1. Remove the element sliding out (nums[i - dist - 1])
            int out = nums[i - dist - 1];
            if (topK.containsKey(out)) {
                windowSum -= out;
                remove(topK, out);
                topKCount--;
            } else {
                remove(candidates, out);
            }

            // 2. Add the new element sliding in (nums[i])
            add(topK, nums[i]);
            windowSum += nums[i];
            topKCount++;

            // 3. Rebalance: ensure topK has exactly k-1 smallest elements
            while (topKCount > k - 1) {
                int maxVal = topK.lastKey();
                remove(topK, maxVal);
                add(candidates, maxVal);
                windowSum -= maxVal;
                topKCount--;
            }
            while (topKCount < k - 1 && !candidates.isEmpty()) {
                int minVal = candidates.firstKey();
                remove(candidates, minVal);
                add(topK, minVal);
                windowSum += minVal;
                topKCount++;
            }
            
            // 4. Critical Swap: If candidates has a smaller value than topK's largest
            if (!candidates.isEmpty() && candidates.firstKey() < topK.lastKey()) {
                int maxTop = topK.lastKey();
                int minCand = candidates.firstKey();
                windowSum = windowSum - maxTop + minCand;
                remove(topK, maxTop);
                remove(candidates, minCand);
                add(topK, minCand);
                add(candidates, maxTop);
            }

            minCost = Math.min(minCost, windowSum);
        }

        return (long) nums[0] + minCost;
    }

    private void add(TreeMap<Integer, Integer> map, int val) {
        map.put(val, map.getOrDefault(val, 0) + 1);
    }

    private void remove(TreeMap<Integer, Integer> map, int val) {
        int count = map.get(val);
        if (count == 1) map.remove(val);
        else map.put(val, count - 1);
    }
}
# 02-02-26
