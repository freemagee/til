---
title: "Subtle art of performance wins"
date: 2019-12-12T09:45:52+00:00
categories:
  - development
tags:
  - meta
  - javascript
---

Today I learned more about the subtle art of javascript performance. I have been doing a few algorithm challenges on [freeCodeCamp](https://www.freecodecamp.org/), which is a great resource. Whilst completing one titled *"Intermediate Algorithm Scripting: Smallest Common Multiple"* I had a look at what other solutions people had posted, and how they compared to mine. A user had put together a CodePen running [BenchmarkJS](https://benchmarkjs.com/) that contained about four solutions. I added my solution (below) to the benchmark test and the results were surprising.
<!--more-->
### My initial solution

```javascript
// smallestCommonsOriginal
function smallestCommons(arr) {
  const [smallest, largest] = arr.sort((a, b) => a - b);
  const dividedByAll = (sum, sequence) => {
    for (let i = sequence[0]; i <= sequence[sequence.length - 1]; i++) {
      if (sum % i !== 0) {
        return false;
      }
    }

    return true;
  };

  const sequence = [];
  // Generate numbers between
  for (let i = smallest; i <= largest; i++) {
    sequence.push(i);
  }

  let lowestCommonFound = false;
  let sum = largest * (largest - 1);

  // loop until the common is found
  while (lowestCommonFound === false) {
    lowestCommonFound = dividedByAll(sum, sequence);
    if (lowestCommonFound) {
      return sum;
    }

    sum = sum + largest;
  }
}

smallestCommons([1, 5]); //should return 60
smallestCommons([5, 1]); //should return 60
smallestCommons([2, 10]); //should return 2520
smallestCommons([1, 13]); //should return 360360
smallestCommons([23, 18]); //should return 6056820
```

### The freeCodeCamp advanced solution

```javascript
const smallestCommonsAdv = arr => {
  let max = Math.max(...arr);
  let min = Math.min(...arr);
  // Initially the solution is assigned to the highest value of the array
  let sol = max;

  for (let i = max - 1; i >= min; i--) {
    // Each time the solution checks (i.e. sol%i===0) it won't be necessary to increment 'max' to our solution and restart the loop
    if (sol % i) {
      sol += max;
      i = max;
    }
  }
  return sol;
};
```

Against the advanced solution to the challenge, my solution ran approx. *2.5x* slower. Which was disappointing!

```javascript
// Running with benchmark.js
smallestCommons x 1,971,923 ops/sec ±1.55% (61 runs sampled)
smallestCommonsAdv x 5,100,845 ops/sec ±0.58% (63 runs sampled)
```

So I proceeded to refactor my solution. I tried a few things - running the loops in reverse and not generating an intermediate sequence. There is more I could have done, but I did not want to completely rewrite the function. This refactor had an improvement, but not as much as I hoped.

### My refactored solution

```javascript
// smallestCommonsRefactor
function smallestCommons(arr) {
  const [min, max] = arr.sort((a, b) => a - b);
  const dividedByAll = (sum, min, max) => {
    for (let i = max; i >= min; i--) {
      if (sum % i !== 0) {
        return false;
      }
    }

    return true;
  };

  let lowestCommonFound = false;
  let sum = max * (max - 1);

  // loop until the common is found
  while (lowestCommonFound === false) {
    lowestCommonFound = dividedByAll(sum, min, max);
    if (lowestCommonFound) {
      return sum;
    }

    sum = sum + max;
  }
}
```

The refactored solution now ran *2.2x* slower than the advanced solution. An improvement of approx. *12%* over my original solution. A slight win 🎉.

```javascript
// Running with benchmark.js
smallestCommonsOriginal x 1,971,923 ops/sec ±1.55% (61 runs sampled)
smallestCommonsRefactor x 2,328,185 ops/sec ±0.70% (61 runs sampled)
smallestCommonsAdv x 5,100,845 ops/sec ±0.58% (63 runs sampled)
```

I looked at the advanced solution. One of the main differences between mine and theirs was the initial sorting of the incoming array. I was using [`Array.prototype.sort`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) whereas they used native maths functions [`Math.max()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max) and [`Math.min()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/min). This got me intrigued. So I refactored once more to include this change.

### My final solution

```javascript
// smallestCommonsMinMax
function smallestCommons(arr) {
  const [min, max] = arr.sort((a, b) => a - b);
  const dividedByAll = (sum, min, max) => {
    for (let i = max; i >= min; i--) {
      if (sum % i !== 0) {
        return false;
      }
    }

    return true;
  };

  let lowestCommonFound = false;
  let sum = max * (max - 1);

  // loop until the common is found
  while (lowestCommonFound === false) {
    lowestCommonFound = dividedByAll(sum, min, max);
    if (lowestCommonFound) {
      return sum;
    }

    sum = sum + max;
  }
}
```

Running *(smallestCommonsMinMax)* this solution in the benchmark suite gave these results.

```javascript
// Running with benchmark.js
smallestCommonsOriginal x 1,971,923 ops/sec ±1.55% (61 runs sampled)
smallestCommonsRefactor x 2,328,185 ops/sec ±0.70% (61 runs sampled)
smallestCommonsMinMax x 4,293,900 ops/sec ±1.34% (62 runs sampled)
smallestCommonsAdv x 5,100,845 ops/sec ±0.58% (63 runs sampled)
```

My final solution *(smallestCommonsMinMax)* was now only *1.18x* slower than the advanced solution. This was an improvement of **52.8%** over my original solution. That is a lot better. Maybe if I continued with this I would eventually write a solution that was faster than the advanced solution, but for now this satisfies my curiousity.

To finish it all off, I wrote a benchmark test to compare `sort` against `min/max`. The results speak for themselves.

```javascript
// Running with benchmark.js
arraySort x 3,183,926 ops/sec ±4.44% (56 runs sampled)
minMax x 10,454,070 ops/sec ±0.52% (63 runs sampled)
```

### The benchmark test

```javascript
function run() {
  const suite = new Benchmark.Suite();
  const output = document.getElementById("console");
  const winner = document.getElementById("winner");

  // Add functions to test
  function arraySort(arr) {
    return arr.sort((a, b) => a - b);
  }

  function minMax(arr) {
    return [Math.min(...arr), Math.max(...arr)];
  }

  // add tests
  const input = [14, 7];

  suite
    .add("arraySort", function() {
      arraySort(input);
    })
    .add("minMax", function() {
      minMax(input);
    })

    // add listeners
    .on("cycle", function(event) {
      const p = document.createElement("p");

      p.innerText = String(event.target);
      output.append(p);
    })
    .on("complete", function() {
      const p = document.createElement("p");

      p.innerHTML = `✨ 👑 Fastest is <strong>${this.filter("fastest").map(
        "name"
      )}</strong> 🦄 ✨`;
      winner.append(p);
    })

    // run async
    .run({ async: true });
}

window.addEventListener("DOMContentLoaded", event => {
  run();
});
```

## In conclusion

I learnt a lot from this, but there are no specific takeaways. Performance is hard, and you really need to understand a programming language deeply to be able to write fast code. Just writing a function that uses one language feature over another could make the function 2.5x slower. 

This is just at the script level. Compound on top of that frameworks, 3rd party plugins, CSS, HTML, browsers, bandwidth, device bottlenecks, etc and it is not surprising that modern web pages can be slow and fair from performant.

But it proves that it is worth it to try and make improvements to the areas you have control over.