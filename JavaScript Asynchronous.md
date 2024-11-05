**JavaScript**, by nature একসাথে অনেকগুলো কাজ করতে পারে না। তাই JavaScript কে বলা হয় single threaded programming language.

এটি যদি একটি উদাহরণের সাথে বলি,

যেমন একজন waiter যদি একটি order নিয়ে kitchen এ গিয়ে সেখানে বসে থাকে এবং রান্নার ready হওয়া পর্যন্ত অপেক্ষা করে। অতঃপর তা নিয়ে `user1` এর কাছে যায়। এই কাজ শেষ করে সে যদি `user2` এর কাছে যায় সুতরাং ইউজার টু ইউজার ওয়ান এর খাবার অর্ডার দেওয়া থেকে খাবার রেডি হয়ে তা আবার তাদের কাছে আসা পর্যন্ত অপেক্ষা করা লাগছে। অর্থাৎ বুঝা যাচ্ছে যে ওয়েটার একবারে একটি কাজই করছে। এখানে যদি আমরা ওয়েটারকে thread হিসেবে ধরি, তাহলে বুঝতে পারি থ্রেড একবারে একটি কাজই করতে পারে। এটিকে বলা হয় synchronous blocking behavior. অর্থাৎ একটি কাজ করার জন্য থ্রেড ব্লক হয়ে থাকে ওপর কাজ করতে পারে না।

```javascript
const processOrder = (customer) => {
  console.log(`processing order for customer 1`); // Task 2

  var currentTime = new Date().getTime();
  while (currentTime + 3000 >= new Date().getTime()); // Task 3

  console.log(`order processed for customer 1`); // Task 4
};

console.log(`take order for customer 1`); // Task 1
processOrder();
console.log(`completed order for customer 1`); // Task 5
```

এখানে,

## Within Browser:

দুইটি জিনিস থাকে।

1. Run time Environment

2. Engine (v8, chakra, gecko)

একটি হল রান টাইম এনভায়রনমেন্ট এবং আরেকটি হলো ইঞ্জিন জাভাস্ক্রিপ্ট রান করে। এখানে বিভিন্ন ব্রাউজারে বিভিন্ন রকম ইঞ্জিন থাকে যেটা ক্রোম ব্রাউজারে ভি8, ইন্টারনেট এক্সপ্লোরারে শাখরা এবং ফায়ারফক্সে গেকো ইঞ্জিন দেখা যায়। যদিও সব ব্রাউজারে রান টাইম এনভায়রনমেন্ট একই থাকে।

### Engine:

জাভাস্ক্রিপ্ট ইঞ্জিনের ভেতর থাকে একটি call stack. নিচে কিভাবে কলস ট্রাকের মধ্যে একে একে উপরের কোডের ফাংশন গুলো রান হবে তা দেখানো হলো।

#### 	Call stack:

1. যেকোনো জাভাস্ক্রিপ্ট কোড রান করার আগে একটি `main();` ফাংশন কল হয়।

2. এবার সবার আগে `console.log(`take order for customer 1`); // Task 1,` অর্থাৎ টাস্ক ওয়ান সম্পূর্ণ হবে।

3. তারপর `processOrder();` নামে যে ফাংশান আছে তার টাস্কগুলো একে একে সম্পন্ন হবে সম্পন্ন হবে:

    1. তার প্রথম লাইন হিসেবে `console.log(`processing order for customer 1`); // Task 2`

    2. এরপর,  
        এই দুটো লাইন এক্সিকিউট হবে। এখানে যতক্ষণ ওয়াইল লুপটি চলবে ততক্ষণ পর্যন্ত কোড এক্সিকিউশন ব্লক অবস্থায় থাকবে। অর্থাৎ আর কোন কিছুই রান করবে না। ইভেন আমরা যদি ক্লিকও করি তাও কাজ করবে না। একেই বলে যাওয়া JavaScript blocking behavior.

        ```javascript
        var currentTime = new Date().getTime();
        while (currentTime + 3000 >= new Date().getTime()); // Task 3
        ```

    3. এবং সবশেষে `console.log(`order processed for customer 1`); // Task 4` এই টাস্কটি সম্পন্ন হবে।

4. `console.log(`completed order for customer 1`); // Task 5` একদম শেষে কোডের লাস্ট লাইনটি প্রিন্ট হবে।

উপরের এই নিয়মেই জাভাস্ক্রিপ্ট এর কোড ব্রাউজারে এক্সিকিউট হয়।



### How to bypass the blocking behavior of JavaScript: 

```javascript
const processOrder = (customer) => {
  console.log(`processing order for customer 1`); // Task 2

  setTimeout(() => {}, 3000);

  console.log(`order processed for customer 1`); // Task 4
};

console.log(`take order for customer 1`); // Task 1
processOrder();
console.log(`completed order for customer 1`); // Task 5
```

উপরের code টি যদি আমরা লক্ষ্য করি, আমরা দেখতে পাচ্ছি যে আগের মত while loop ব্যবহার করা হয়নি। বরং `setTimeout()` function ব্যবহার করা হয়েছে।

এবার এই process টি কিভাবে সংঘটিত হবে তা দেখা যাক:

#### Within Call Stack:

1. প্রথমে, `console.log(`take order for customer 1`); // Task 1`

2. `processOrder();`

    1. `console.log(`processing order for customer 1`); // Task 2`

    2. এবার আগের বারের চেয়ে একটু ভিন্ন কিছু হবে। যে `setTimeout(() =&gt; {}, 3000);` ফাংশনটি আছে তা পুরো কোড কে ব্লক না করে দিয়ে বরং তা Web API এর কাছে পাঠিয়ে দিচ্ছে।

        #### Within Web Api (run time environment একটি অংশ):

        `setTimeout(() =&gt; {}, 3000);` এই ফাংশনটির রান হবে যার সাথে call stack এর কোন সম্পর্ক নেই।

    3.



