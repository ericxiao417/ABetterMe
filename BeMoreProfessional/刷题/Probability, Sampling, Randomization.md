# Probability, Sampling, Randomization

## Given a stream, how to keep track of the median of the numbers read so far?

分析：

Data structure: 

1. max heap: keep the smaller half 
2. min heap: keep the larger half

Two properties:

1. either size(max heap) == size(min heap) or size(max heap) = size(min heap) + 1.
2. max_heap.peek() <= min_heap.peek().

For each step:

Step 1 - Compare: x <= min_heap.peek(), insert x into max_heap. If x > max_heap.peek(), then insert x into min_heap.

Step 2 - adjust the size: 

if size(max_heap) ==  size(min_heap) or size(max_heap) == size(min_heap) + 1, skip; 

if size(max_heap) > size(min_heap) + 1， move the max_heap.poll() to min_heap; 

if size(max_heap) < size(min_heap), move min_heap.poll() to max_heap.

Median result: 

1. if even, (max_heap.peek() + min_heap.peek()) / 2;
2. if odd, min_heap.peek().