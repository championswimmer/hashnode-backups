---
title: "Checking SotA LLM Models trying to solve a net-new LeetCode style puzzle"
datePublished: Thu Jan 23 2025 01:56:40 GMT+0000 (Coordinated Universal Time)
cuid: cm68omnat000108icgb6n6wcu
slug: checking-sota-llm-models-trying-to-solve-a-net-new-leetcode-style-puzzle
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ln2J1t5zGYo/upload/13849f00e0ef2c4fc8e0df08b69a7f9e.jpeg
tags: artificial-intelligence, model-evaluation, leetcode, llm

---

I am sure a lot of you would have seen this particular meme template. It has given rise to entire genre of tiktoks where girls are amazed at how much calculation guys do to pick which stall to go to in a crowded row of urinals.

![High Quality Urinal Guy (More text room) Blank Meme Template](https://i.imgflip.com/12msgn.jpg align="center")

I actually even made a whole game to place people on urinal stalls using various code-generating tools like [bolt.new](https://bolt.new) to have fun with this

%[https://twitter.com/championswimmer/status/1881512904161415625] 

## The Prompt

Anyway, that led me to wonder, how would today’s (that is Jan 2025) state of the art LLM models fare on solving a LeetCode-style puzzle of optimally placing people in a row of urinal stalls which are already partially occupied.

So I tried my best to come up with a prompt that’s worded like typical LeetCode questions

> There are 8 urinal stalls and some people are already using some of them. We have to place one new person in that stall. The current state of the urinal stalls is represented by a 8 digit number like 01100110 which means there are people occupying stalls 2, 3,6 and 7 After the new person is placed at a position, the final "comfort" score is calculated. We go from one end to another, and for every person (i.e. a '1') we do the following
> 
> * add +1 for empty slot on either left or right (+2 if both empty)
>     
> * add another +1 for 2 empty slots on either left or right (+2 if on both sides)
>     
> 
> For the following existing urinal stall configurations, give the answer that in which position should the new person be added, and what will be the total comfort score of that configurations. If there are more than one positions where the highest comfort score can be achieved, any of those positions is a correct answer. (stalls are counted 1-indexed, starting from left).
> 
> Question Inputs
> 
> 1. 01101010
>     
> 2. 11010101
>     
> 3. 11001011
>     
> 4. 00111100
>     
> 5. 01001010
>     
> 6. 11001110
>     
> 7. 10000110
>     
> 8. 11000110
>     

This particular question is something I just conjured up 30 min ago, so obviously this exact question would not be in the training data. But the way this question is framed with the particular format of input and output, is fairly common from LeetCode to events like Advent of Code, so the LLMs are definitely trained on tons of data of this particular format.

## The Answers

Let’s see how different LLM models fare with this prompt. These are all 1st-attempt answers, on a fresh chat without any previous context, conversation history or “memory”.

### Claude 3.5 Sonnet

```markdown
I'll help solve this urinal placement optimization problem. Let me solve it step by step for each configuration.

1. For 01101010:
Let me check each possible position and calculate scores.
The best position is stall 8 (rightmost), giving a final configuration of 01101011.
Final score calculation:
- Position 2: +1 (right empty)
- Position 3: +1 (right empty)
- Position 6: +2 (both sides empty)
- Position 7: +1 (right empty)
- Position 8: +1 (left empty)
Total comfort score: 6

2. For 11010101:
Only positions 4 and 6 are available.
Best position is 4, giving 11011101.
Final score calculation:
- Position 1: +1 (right empty)
- Position 2: +1 (right empty)
- Position 4: +2 (initially both empty)
- Position 6: +1 (right empty)
- Position 8: +1 (left empty)
Total comfort score: 6

3. For 11001011:
Position 5 is the best choice, giving 11001111.
Final score calculation:
- Position 1: +1 (right empty)
- Position 2: +1 (right empty)
- Position 5: +0 (no empty adjacent)
- Position 6: +0 (no empty adjacent)
- Position 7: +0 (no empty adjacent)
- Position 8: +1 (left empty)
Total comfort score: 3

4. For 00111100:
Position 1 is optimal, giving 10111100.
Final score calculation:
- Position 1: +1 (right empty)
- Position 3: +0 (no empty adjacent)
- Position 4: +0 (no empty adjacent)
- Position 5: +0 (no empty adjacent)
- Position 6: +1 (right empty)
Total comfort score: 2

5. For 01001010:
Position 8 is optimal, giving 01001011.
Final score calculation:
- Position 2: +2 (both sides empty)
- Position 5: +2 (both sides empty)
- Position 7: +1 (right empty)
- Position 8: +1 (left empty)
Total comfort score: 6

6. For 11001110:
Position 5 is optimal, giving 11001110.
Final score calculation:
- Position 1: +1 (right empty)
- Position 2: +1 (right empty)
- Position 5: +0 (no empty adjacent)
- Position 6: +1 (right empty)
- Position 7: +1 (right empty)
Total comfort score: 4

7. For 10000110:
Position 4 is optimal, giving 10001110.
Final score calculation:
- Position 1: +2 (right empty + double right empty)
- Position 4: +2 (both sides empty)
- Position 6: +1 (right empty)
- Position 7: +1 (right empty)
Total comfort score: 6

8. For 11000110:
Position 4 is optimal, giving 11001110.
Final score calculation:
- Position 1: +1 (right empty)
- Position 2: +1 (right empty)
- Position 4: +1 (right empty)
- Position 6: +1 (right empty)
- Position 7: +1 (right empty)
Total comfort score: 5

Summary of answers:
1. Position 8, Score 6
2. Position 4, Score 6
3. Position 5, Score 3
4. Position 1, Score 2
5. Position 8, Score 6
6. Position 5, Score 4
7. Position 4, Score 6
8. Position 4, Score 5
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">⚠</div>
<div data-node-type="callout-text">The answers are mostly wrong. Sometimes they calculations seem to track well, until it doesn’t. For many cases it says “Position X, +1 (right empty)” when clearly there is a 1 to the right of that slot.</div>
</div>

### Gemini 1.5 Pro

```markdown
**Understanding the Scoring**

* **Proximity:** Each occupied stall (1) gets points for adjacent empty stalls (0).  One point for each side with an empty stall, up to two points per side.
* **Total Score:** The sum of the proximity points for all occupied stalls.

**Analyzing the Inputs**

To find the best position for the new person, we'll consider each possible placement and calculate the resulting comfort score.

**1. 01101010**

* **Best Position:** 5 (0110**1**1010)
* **Score:** 10 
    * (Stall 2: 2 points) + (Stall 3: 2 points) + (Stall 5: 2 points) + (Stall 7: 2 points) + (Stall 8: 2 points) = 10

**2. 11010101**

* **Best Position:** 3 (11**1**10101) 
* **Score:** 6
    * (Stall 1: 1 point) + (Stall 2: 1 point) + (Stall 3: 2 points) + (Stall 4: 1 point) + (Stall 6: 1 point) + (Stall 8: 0 points) = 6

**3. 11001011**

* **Best Position:**  3 (11**1**01011)
* **Score:** 8
    * (Stall 1: 1 point) + (Stall 2: 1 point) + (Stall 3: 2 points) + (Stall 5: 2 points) + (Stall 7: 1 point) + (Stall 8: 1 point) = 8

**4. 00111100**

* **Best Positions:** 1 or 8 (**1**0111100 or 0011110**1**)
* **Score:** 8
    * (Stall 1/8: 2 points) + (Stall 3: 2 points) + (Stall 4: 2 points) + (Stall 5: 2 points) = 8

**5. 01001010**

* **Best Position:** 3 (01**1**01010)
* **Score:** 10
    * (Stall 2: 2 points) + (Stall 3: 2 points) + (Stall 5: 2 points) + (Stall 7: 2 points) + (Stall 8: 2 points) = 10

**6. 11001110**

* **Best Position:** 3 (11**1**01110)
* **Score:** 6
    * (Stall 1: 1 point) + (Stall 2: 1 point) + (Stall 3: 2 points) + (Stall 5: 1 point) + (Stall 6: 1 point) + (Stall 7: 0 points) = 6

**7. 10000110**

* **Best Position:** 4 (100**1**0110)
* **Score:** 10
    * (Stall 1: 2 points) + (Stall 4: 4 points) + (Stall 6: 2 points) + (Stall 7: 2 points) = 10

**8. 11000110**

* **Best Position:**  4 (110**1**0110)
* **Score:** 8
    * (Stall 1: 1 point) + (Stall 2: 1 point) + (Stall 4: 4 points) + (Stall 6: 1 point) + (Stall 7: 1 point) = 8 
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">🚨</div>
<div data-node-type="callout-text">The calculations seem mostly wrong. It does make sure to calculate only for occupied slots, and not for the empty slots, so the calculations aren’t “random”, just wrong.</div>
</div>

### Gemini 2.0 Flash Experimental

```markdown
Here's the breakdown of optimal placement and comfort scores for each urinal configuration:

**Understanding the Scoring**

It's crucial to understand the scoring system. For each occupied stall (1), we check the adjacent empty stalls (0s).

*   **+1 for each adjacent empty stall:** Up to +2 per occupied stall.
*   **+1 for each pair of adjacent empty stalls:** Up to +2 per occupied stall.

**Calculations**

Here's the analysis for each configuration:

**1. 01101010**

*   Placing at index 0: 11101010. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   Placing at index 3: 01111010. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   Placing at index 5: 01101110. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   Placing at index 7: 01101011. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   **Optimal Placement:** 0, 3, 5, or 7.
*   **Comfort Score:** 8

**2. 11010101**

*   Placing at index 2: 11110101. Score: (0+1)+(1+1)+(1+1)+(1+1) = 6
*   Placing at index 4: 11011101. Score: (1+1)+(1+1)+(1+1)+(0+1) = 6
*   Placing at index 6: 11010111. Score: (1+1)+(1+1)+(0+1)+(1+1) = 6
*   **Optimal Placement:** 2, 4, or 6.
*   **Comfort Score:** 6

**3. 11001011**

*   Placing at index 2: 11101011. Score: (0+1)+(1+2)+(1+1)+(1+1) = 7
*   Placing at index 3: 11011011. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   Placing at index 5: 11001111. Score: (1+1)+(1+1)+(0+1)+(1+1) = 6
*   **Optimal Placement:** 3
*   **Comfort Score:** 8

**4. 00111100**

*   Placing at index 0: 10111100. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   Placing at index 1: 01111100. Score: (1+0)+(0+1)+(1+1)+(1+1) = 6
*   Placing at index 6: 00111110. Score: (1+1)+(1+1)+(1+0)+(0+1) = 6
*   Placing at index 7: 00111101. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   **Optimal Placement:** 0 or 7
*   **Comfort Score:** 8

**5. 01001010**

*   Placing at index 0: 11001010. Score: (0+2)+(1+1)+(1+1)+(1+1) = 7
*   Placing at index 2: 01101010. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   Placing at index 3: 01011010. Score: (1+1)+(0+2)+(1+1)+(1+1) = 7
*   Placing at index 5: 01001110. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   Placing at index 7: 01001011. Score: (1+1)+(1+1)+(1+1)+(1+1) = 8
*   **Optimal Placement:** 2, 5, or 7
*   **Comfort Score:** 8

**6. 11001110**

*   Placing at index 2: 11101110. Score: (0+1)+(1+2)+(0+1)+(0+1) = 5
*   Placing at index 3: 11011110. Score: (1+1)+(1+1)+(0+1)+(0+1) = 6
*   **Optimal Placement:** 3
*   **Comfort Score:** 6

**7. 10000110**

*   Placing at index 1: 11000110. Score: (0+1)+(2+1)+(1+1) = 6
*   Placing at index 2: 10100110. Score: (1+1)+(1+1)+(1+1) = 6
*   Placing at index 3: 10010110. Score: (1+1)+(1+1)+(1+1) = 6
*   Placing at index 4: 10001110. Score: (1+1)+(0+1)+(0+1) = 4
*   **Optimal Placement:** 1, 2, or 3
*   **Comfort Score:** 6

**8. 11000110**

*   Placing at index 2: 11100110. Score: (0+1)+(2+1)+(1+1) = 6
*   Placing at index 3: 11010110. Score: (1+1)+(1+1)+(1+1) = 6
*   Placing at index 4: 11001110. Score: (1+1)+(0+1)+(0+1) = 4
*   **Optimal Placement:** 2 or 3
*   **Comfort Score:** 6
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">🚫</div>
<div data-node-type="callout-text">This one tries to give multiple valid answers for each one. But the actual answers are again fairly wrong.</div>
</div>

### Gemini 2.0 Experimental Advanced

```markdown
Okay, I understand the problem. Let's analyze each configuration and determine the best position for the new person and the resulting comfort score.

**How the Comfort Score is Calculated**

For each person (1) in the urinal row:

*   **+1 point:**  If there's an empty slot (0) to their immediate left or right.
*   **+1 point (additional):** If there are two empty slots (00) to their immediate left or right.

**Analysis of Each Configuration**

Here's the breakdown for each input:

**1. 01101010**

*   **Possible new person positions:** 1, 4, 6, 8
*   **Analysis:**
    *   **Position 1:** `11101010` -> Comfort: 0 + 1 + 2 + 0 + 1 + 0 + 1 + 0 = 5
    *   **Position 4:** `01111010` -> Comfort: 0 + 1 + 2 + 2 + 2 + 0 + 1 + 0 = 8
    *   **Position 6:** `01101110` -> Comfort: 0 + 1 + 1 + 0 + 1 + 2 + 2 + 0 = 7
    *   **Position 8:** `01101011` -> Comfort: 0 + 1 + 1 + 0 + 1 + 0 + 1 + 1 = 5
*   **Best position:** 4
*   **Comfort score:** 8

**2. 11010101**

*   **Possible new person positions:** 3, 5, 7
*   **Analysis:**
    *   **Position 3:** `11110101` -> Comfort: 1 + 2 + 2 + 2 + 0 + 1 + 0 + 1 = 9
    *   **Position 5:** `11011101` -> Comfort: 1 + 1 + 0 + 1 + 2 + 2 + 0 + 1 = 8
    *   **Position 7:** `11010111` -> Comfort: 1 + 1 + 0 + 1 + 0 + 1 + 2 + 2 = 8
*   **Best position:** 3
*   **Comfort score:** 9

**3. 11001011**

*   **Possible new person positions:** 3, 4, 6
*   **Analysis:**
    *   **Position 3:** `11101011` -> Comfort: 1 + 2 + 1 + 0 + 1 + 0 + 2 + 1 = 8
    *   **Position 4:** `11011011` -> Comfort: 1 + 1 + 0 + 2 + 2 + 0 + 2 + 1 = 9
    *   **Position 6:** `11001111` -> Comfort: 1 + 1 + 0 + 0 + 1 + 2 + 2 + 2 = 9
*   **Best positions:** 4, 6
*   **Comfort score:** 9

**4. 00111100**

*   **Possible new person positions:** 1, 2, 7, 8
*   **Analysis:**
    *   **Position 1:** `10111100` -> Comfort: 1 + 0 + 2 + 2 + 2 + 1 + 0 + 0 = 8
    *   **Position 2:** `01111100` -> Comfort: 0 + 1 + 2 + 2 + 2 + 1 + 0 + 0 = 8
    *   **Position 7:** `00111110` -> Comfort: 0 + 0 + 1 + 2 + 2 + 2 + 1 + 0 = 8
    *   **Position 8:** `00111101` -> Comfort: 0 + 0 + 1 + 2 + 2 + 1 + 0 + 1 = 7
*   **Best positions:** 1, 2, 7
*   **Comfort score:** 8

**5. 01001010**

*   **Possible new person positions:** 1, 3, 4, 6, 8
*   **Analysis:**
    *   **Position 1:** `11001010` -> Comfort: 1 + 2 + 0 + 0 + 1 + 0 + 1 + 0 = 5
    *   **Position 3:** `01101010` -> Comfort: 0 + 1 + 2 + 0 + 1 + 0 + 1 + 0 = 5
    *   **Position 4:** `01011010` -> Comfort: 0 + 1 + 0 + 2 + 2 + 0 + 1 + 0 = 6
    *   **Position 6:** `01001110` -> Comfort: 0 + 1 + 0 + 0 + 1 + 2 + 2 + 0 = 6
    *   **Position 8:** `01001011` -> Comfort: 0 + 1 + 0 + 0 + 1 + 0 + 1 + 1 = 4
*   **Best positions:** 4, 6
*   **Comfort score:** 6

**6. 11001110**

*   **Possible new person positions:** 3, 4, 8
*   **Analysis:**
    *   **Position 3:** `11101110` -> Comfort: 1 + 2 + 1 + 0 + 2 + 2 + 2 + 0 = 10
    *   **Position 4:** `11011110` -> Comfort: 1 + 1 + 0 + 2 + 2 + 2 + 2 + 0 = 10
    *   **Position 8:** `11001111` -> Comfort: 1 + 1 + 0 + 0 + 1 + 2 + 2 + 2 = 9
*   **Best positions:** 3, 4
*   **Comfort score:** 10

**7. 10000110**

*   **Possible new person positions:** 2, 3, 4, 5, 8
*   **Analysis:**
    *   **Position 2:** `11000110` -> Comfort: 1 + 2 + 0 + 0 + 0 + 2 + 1 + 0 = 6
    *   **Position 3:** `10100110` -> Comfort: 1 + 0 + 2 + 0 + 0 + 2 + 1 + 0 = 6
    *   **Position 4:** `10010110` -> Comfort: 1 + 0 + 0 + 2 + 0 + 2 + 1 + 0 = 6
    *   **Position 5:** `10001110` -> Comfort: 1 + 0 + 0 + 0 + 2 + 2 + 2 + 0 = 7
    *   **Position 8:** `10000111` -> Comfort: 1 + 0 + 0 + 0 + 0 + 1 + 2 + 2 = 6
*   **Best position:** 5
*   **Comfort score:** 7

**8. 11000110**

*   **Possible new person positions:** 3, 4, 5, 8
*   **Analysis:**
    *   **Position 3:** `11100110` -> Comfort: 1 + 2 + 1 + 0 + 0 + 2 + 1 + 0 = 7
    *   **Position 4:** `11010110` -> Comfort: 1 + 1 + 0 + 2 + 0 + 2 + 1 + 0 = 7
    *   **Position 5:** `11001110` -> Comfort: 1 + 1 + 0 + 0 + 2 + 2 + 2 + 0 = 8
    *   **Position 8:** `11000111` -> Comfort: 1 + 1 + 0 + 0 + 0 + 1 + 2 + 2 = 7
*   **Best position:** 5
*   **Comfort score:** 8

**Summary Table**

| Input      | Best Position(s) | Comfort Score |
| :--------- | :--------------- | :------------ |
| 01101010   | 4                | 8             |
| 11010101   | 3                | 9             |
| 11001011   | 4, 6             | 9             |
| 00111100   | 1, 2, 7          | 8             |
| 01001010   | 4, 6             | 6             |
| 11001110   | 3, 4             | 10            |
| 10000110   | 5                | 7             |
| 11000110   | 5                | 8             |
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">🗣</div>
<div data-node-type="callout-text">This is the first model which seems to at least narrate back the scoring logic coherently (and addmittedly more clearly than it is described in my question itself). But despite that, the actual scoring for the data is still completely wrong.</div>
</div>

### Chat GPT 4o

```markdown
To solve this problem, we'll compute the total "comfort" score for each valid position where the new person can be placed and find the position that yields the highest comfort score. Here's the solution for each input configuration:

---

### **1. Input: `01101010`**
- Valid positions: 1, 5, 8.
- Comfort scores:
  - Place at position **1**: Final state `11101010`. Score = 8.
  - Place at position **5**: Final state `01101110`. Score = 8.
  - Place at position **8**: Final state `01101011`. Score = 8.
- **Answer**: Position 1 (or 5 or 8), **Comfort score: 8**.

---

### **2. Input: `11010101`**
- Valid positions: 3, 6.
- Comfort scores:
  - Place at position **3**: Final state `11110101`. Score = 7.
  - Place at position **6**: Final state `11011101`. Score = 7.
- **Answer**: Position 3 (or 6), **Comfort score: 7**.

---

### **3. Input: `11001011`**
- Valid positions: 4, 5.
- Comfort scores:
  - Place at position **4**: Final state `11011011`. Score = 8.
  - Place at position **5**: Final state `11001111`. Score = 8.
- **Answer**: Position 4 (or 5), **Comfort score: 8**.

---

### **4. Input: `00111100`**
- Valid positions: 1, 2, 7, 8.
- Comfort scores:
  - Place at position **1**: Final state `10111100`. Score = 4.
  - Place at position **2**: Final state `01111100`. Score = 4.
  - Place at position **7**: Final state `00111110`. Score = 4.
  - Place at position **8**: Final state `00111101`. Score = 4.
- **Answer**: Position 1 (or 2, 7, 8), **Comfort score: 4**.

---

### **5. Input: `01001010`**
- Valid positions: 1, 3, 5, 7, 8.
- Comfort scores:
  - Place at position **1**: Final state `11001010`. Score = 10.
  - Place at position **3**: Final state `01101010`. Score = 9.
  - Place at position **5**: Final state `01011010`. Score = 9.
  - Place at position **7**: Final state `01001110`. Score = 9.
  - Place at position **8**: Final state `01001011`. Score = 9.
- **Answer**: Position 1, **Comfort score: 10**.

---

### **6. Input: `11001110`**
- Valid positions: 5.
- Comfort scores:
  - Place at position **5**: Final state `11011110`. Score = 7.
- **Answer**: Position 5, **Comfort score: 7**.

---

### **7. Input: `10000110`**
- Valid positions: 2, 3, 4, 5.
- Comfort scores:
  - Place at position **2**: Final state `11000110`. Score = 10.
  - Place at position **3**: Final state `10100110`. Score = 9.
  - Place at position **4**: Final state `10010110`. Score = 9.
  - Place at position **5**: Final state `10001110`. Score = 9.
- **Answer**: Position 2, **Comfort score: 10**.

---

### **8. Input: `11000110`**
- Valid positions: 4, 5.
- Comfort scores:
  - Place at position **4**: Final state `11010110`. Score = 8.
  - Place at position **5**: Final state `11001110`. Score = 7.
- **Answer**: Position 4, **Comfort score: 8**.

---

### **Summary of Results**
1. **01101010** → Position 1 (or 5, 8), Score = 8.  
2. **11010101** → Position 3 (or 6), Score = 7.  
3. **11001011** → Position 4 (or 5), Score = 8.  
4. **00111100** → Position 1 (or 2, 7, 8), Score = 4.  
5. **01001010** → Position 1, Score = 10.  
6. **11001110** → Position 5, Score = 7.  
7. **10000110** → Position 2, Score = 10.  
8. **11000110** → Position 4, Score = 8.
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">❗</div>
<div data-node-type="callout-text">Once again, this is also entirely wrong. It even calculated wrong “valid positions”. For example in the first case, 5 is not a valid position to insert the new person.</div>
</div>

### GPT o1 Preview

Finally it is time for our “thinking” model to have a go at it. It thought for close to 3 minutes for this.

```markdown
To solve this problem, for each urinal stall configuration we considered all vacant positions (positions where the value is '0') and calculated the comfort score after placing a new person in that position. The comfort score is determined by the sum of comfort levels of all occupied stalls after the new person is placed, where the comfort for each person is calculated based on adjacent empty stalls as per the given rules.

For each configuration, we compared the total comfort scores for all possible placements of the new person and selected the position that resulted in the highest comfort score. If multiple positions yield the same highest score, any one of those positions can be the answer.

Here are the answers for the given configurations:

1. **Position:** 7, **Comfort Score:** 6
2. **Position:** 3, **Comfort Score:** 4
3. **Position:** 6, **Comfort Score:** 4
4. **Position:** 1, **Comfort Score:** 2
5. **Position:** 1, **Comfort Score:** 7
6. **Position:** 8, **Comfort Score:** 4
7. **Position:** 3, **Comfort Score:** 7
8. **Position:** 3, **Comfort Score:** 5

istration of the new person should be in the following stalls with the corresponding comfort scores.

**Answers:**

1. 7 6
2. 3 4
3. 6 4
4. 1 2
5. 1 7
6. 8 4
7. 3 7
8. 3 5
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">🧠</div>
<div data-node-type="callout-text">Finally, at least some correct answers! Except it did get the first input wrong. Position 7 is already occupied. But it got rest of the inputs correct and calculated the score correctly too! There’s a type/incomplete word “istration”, not sure how that happened in the output.</div>
</div>

## Result Summary

I don’t know about the “AGI” hype of o1, but it definitely marks itself a rank above the others, when faced with a net-new question, which it never faced in training data. If the chain-of-thought was published, it would definitely be intriguing to look at, but at least on the board it scores a 7:1 win, when the closest anyone else could do (Claude 3.5 Sonnet) was a 1:7 loss.

| **Model** | **Correct Solution** | **Remarks** |
| --- | --- | --- |
| Claude 3.5 Sonnet | 1 ✅ 7 ❌ | Wrong calculations and answers, but one input solved (with correct explanation and breakdown) |
| Gemini 1.5 Pro | ❌ | Explained back the logic, but all calculations and answers wrong |
| Gemini 2.0 Flash Exp | ❌ | Explained back the logic, but all calculations and answers wrong |
| Gemini 2.0 Exp Adv | ❌ | Explained back the logic, but all calculations and answers wrong |
| GPT 4o | ❌ | All calculations and answers wrong |
| GPT o1 Preview | 1 ❌ 7 ✅ | &lt;think&gt; for 3 min. Explanations not given in final answers, but gave correct position and score for all inputs except the first one. |

If you try this prompt out with any open models like **DeepSeek R1**, **LLaMa, Mistral** etc, let me know in comments how did it go and did any of them solve it.