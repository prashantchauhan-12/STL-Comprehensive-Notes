# C++ STL Comprehensive Notes for Competitive Programming

> **Quick Reference Guide** - Everything you need for DSA and Competitive Programming

---

## Table of Contents
1. [Containers](#containers)
2. [Iterators](#iterators)
3. [Algorithms](#algorithms)
4. [Utility Functions](#utility-functions)
5. [String Operations](#string-operations)
6. [Numeric Operations](#numeric-operations)
7. [Advanced Techniques](#advanced-techniques)
8. [Time & Space Complexity](#complexity-reference)

---

## Containers

### 1. Vector (Dynamic Array)
```cpp
#include <vector>

// Declaration
vector<int> v;
vector<int> v(n);           // size n, default initialized
vector<int> v(n, val);      // size n, all elements = val
vector<int> v = {1, 2, 3};  // initializer list
vector<vector<int>> matrix(n, vector<int>(m, 0)); // 2D vector

// Access
v[i];           // O(1) - no bounds checking
v.at(i);        // O(1) - with bounds checking
v.front();      // first element
v.back();       // last element

// Modifiers
v.push_back(x);         // O(1) amortized
v.pop_back();           // O(1)
v.insert(v.begin()+i, x);  // O(n) - insert at position i
v.erase(v.begin()+i);      // O(n) - erase at position i
v.erase(v.begin()+i, v.begin()+j); // erase range [i, j)
v.clear();              // O(n)
v.resize(n);            // resize to n
v.resize(n, val);       // resize to n with value val

// Capacity
v.size();       // current size
v.capacity();   // allocated capacity
v.empty();      // check if empty
v.reserve(n);   // reserve capacity for n elements

// Useful tricks
reverse(v.begin(), v.end());
sort(v.begin(), v.end());
sort(v.begin(), v.end(), greater<int>()); // descending
```

**Time Complexity:**
- Access: O(1)
- Insert/Delete at end: O(1) amortized
- Insert/Delete at middle: O(n)
- Search: O(n)

---

### 2. Deque (Double-ended Queue)
```cpp
#include <deque>

deque<int> dq;

// All vector operations +
dq.push_front(x);   // O(1)
dq.pop_front();     // O(1)

// Use when you need efficient insertion/deletion at both ends
```

---

### 3. Stack (LIFO)
```cpp
#include <stack>

stack<int> st;

st.push(x);     // O(1)
st.pop();       // O(1)
st.top();       // O(1) - access top element
st.empty();     // check if empty
st.size();      // number of elements

// No iterators available
```

**Common Uses:**
- Expression evaluation
- Backtracking
- Monotonic stack problems
- DFS implementation

---

### 4. Queue (FIFO)
```cpp
#include <queue>

queue<int> q;

q.push(x);      // O(1) - enqueue
q.pop();        // O(1) - dequeue
q.front();      // O(1) - first element
q.back();       // O(1) - last element
q.empty();
q.size();

// No iterators available
```

**Common Uses:**
- BFS
- Level order traversal
- Sliding window problems

---

### 5. Priority Queue (Heap)
```cpp
#include <queue>

// MAX HEAP (default)
priority_queue<int> pq;
priority_queue<int, vector<int>> pq;

// MIN HEAP
priority_queue<int, vector<int>, greater<int>> pq;
priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq; // shorter

// Custom comparator - Structure
struct comp {
    bool operator()(int &a, int &b) {
        return a < b; // max-heap (parent > child, so return a < b)
        // return a > b; // min-heap
    }
};
priority_queue<int, vector<int>, comp> pq;

// Custom comparator - Function
static bool comp(int &a, int &b) {
    return a < b; // max-heap
}
priority_queue<int, vector<int>, function<bool(int&, int&)>> pq(comp);

// Custom comparator - Lambda
auto comp = [](int &a, int &b) {
    return a < b; // max-heap
};
priority_queue<int, vector<int>, decltype(comp)> pq(comp);

// Lambda with capture (accessing external variables)
unordered_map<int, int> mp;
auto comp = [&mp](int &a, int &b) {
    return mp[a] < mp[b];
};
priority_queue<int, vector<int>, decltype(comp)> pq(comp);

// Operations
pq.push(x);     // O(log n)
pq.pop();       // O(log n)
pq.top();       // O(1)
pq.empty();
pq.size();
```

**Important:** For custom comparators, remember:
- `return a < b` → max-heap (larger elements have higher priority)
- `return a > b` → min-heap (smaller elements have higher priority)

---

### 6. Set (Ordered, Unique)
```cpp
#include <set>

set<int> s;
multiset<int> ms;  // allows duplicates

// Insert
s.insert(x);        // O(log n)
s.insert({1,2,3});  // multiple elements

// Delete
s.erase(x);         // O(log n) - erase by value
s.erase(s.find(x)); // O(log n) - erase by iterator
s.clear();          // O(n)

// Search
s.find(x);          // O(log n) - returns iterator
s.count(x);         // O(log n) - returns 0 or 1 for set, count for multiset

// Access
*s.begin();         // smallest element
*s.rbegin();        // largest element

// Bounds
s.lower_bound(x);   // iterator to first element >= x
s.upper_bound(x);   // iterator to first element > x

// Size
s.size();
s.empty();

// Iteration
for(auto x : s) { }
for(auto it = s.begin(); it != s.end(); it++) { }
```

**Multiset specific:**
```cpp
multiset<int> ms;
ms.erase(x);           // erases ALL occurrences
ms.erase(ms.find(x));  // erases only ONE occurrence
```

---

### 7. Unordered Set (Hash Set)
```cpp
#include <unordered_set>

unordered_set<int> us;
unordered_multiset<int> ums;

// Same operations as set but:
// - Average O(1) for insert, erase, find
// - Worst case O(n)
// - No ordering
// - No lower_bound/upper_bound
```

**When to use:**
- Need O(1) operations
- Don't need ordering
- Don't need range queries

---

### 8. Map (Ordered Key-Value)
```cpp
#include <map>

map<int, int> mp;
map<string, vector<int>> mp;
multimap<int, int> mmp;  // allows duplicate keys

// Insert
mp[key] = value;        // O(log n)
mp.insert({key, value});
mp[key]++;              // auto-initializes to 0 if not exists

// Access
mp[key];                // creates if not exists
mp.at(key);             // throws exception if not exists

// Delete
mp.erase(key);          // O(log n)
mp.clear();

// Search
mp.find(key);           // O(log n) - returns iterator
mp.count(key);          // O(log n) - returns 0 or 1

// Bounds
mp.lower_bound(key);    // first element with key >= given key
mp.upper_bound(key);    // first element with key > given key

// Iteration
for(auto &[key, val] : mp) { }  // C++17
for(auto &p : mp) { 
    int key = p.first;
    int val = p.second;
}

// Size
mp.size();
mp.empty();
```

**Useful trick - Lambda in map:**
```cpp
unordered_map<string, function<int(int, int)>> operations = {
    {"+", [](int a, int b) { return a + b; }},
    {"-", [](int a, int b) { return a - b; }},
    {"*", [](int a, int b) { return a * b; }},
    {"/", [](int a, int b) { return a / b; }}
};

int result = operations["+"](5, 3); // returns 8
```

---

### 9. Unordered Map (Hash Map)
```cpp
#include <unordered_map>

unordered_map<int, int> ump;
unordered_multimap<int, int> ummp;

// Same operations as map but:
// - Average O(1) for insert, erase, find
// - Worst case O(n)
// - No ordering
// - No lower_bound/upper_bound
```

**When to use:**
- Need O(1) operations
- Don't need ordering
- Don't need range queries

---

### 10. Pair
```cpp
#include <utility>

pair<int, int> p;
pair<int, string> p = {1, "hello"};
pair<int, int> p = make_pair(1, 2);

// Access
p.first;
p.second;

// Comparison (lexicographic)
pair<int,int> p1 = {1, 2};
pair<int,int> p2 = {1, 3};
p1 < p2;  // true (compares first, then second)

// Useful in sorting
vector<pair<int,int>> v;
sort(v.begin(), v.end()); // sorts by first, then second
```

---

### 11. Tuple (C++11)
```cpp
#include <tuple>

tuple<int, string, double> t = make_tuple(1, "hello", 3.14);
tuple<int, string, double> t = {1, "hello", 3.14};

// Access
get<0>(t);  // 1
get<1>(t);  // "hello"
get<2>(t);  // 3.14

// Tie (unpack)
int x;
string s;
double d;
tie(x, s, d) = t;

// Ignore some values
tie(x, ignore, d) = t;

// C++17 structured binding
auto [x, s, d] = t;
```

---

## Iterators

```cpp
// Types
vector<int>::iterator it;
vector<int>::reverse_iterator rit;
vector<int>::const_iterator cit;

// Common operations
it++;           // next element
it--;           // previous element
*it;            // dereference
it + n;         // advance by n (random access only)
it - n;         // go back by n (random access only)

// Standard iterators
v.begin();      // first element
v.end();        // past-the-last element
v.rbegin();     // reverse begin (last element)
v.rend();       // reverse end (before first)

// Distance
distance(it1, it2);  // number of elements between iterators

// Advance
advance(it, n);      // move iterator by n positions
```

---

## Algorithms

### Sorting
```cpp
#include <algorithm>

vector<int> v = {3, 1, 4, 1, 5};

// Sort ascending
sort(v.begin(), v.end());  // O(n log n)

// Sort descending
sort(v.begin(), v.end(), greater<int>());

// Custom comparator
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;  // descending
});

// Sort pairs
vector<pair<int,int>> vp;
sort(vp.begin(), vp.end());  // sorts by first, then second

// Custom sort for pairs
sort(vp.begin(), vp.end(), [](auto &a, auto &b) {
    if(a.first == b.first) return a.second > b.second;
    return a.first < b.first;
});

// Partial sort (sort first k elements)
partial_sort(v.begin(), v.begin()+k, v.end());

// Check if sorted
is_sorted(v.begin(), v.end());

// Stable sort (maintains relative order of equal elements)
stable_sort(v.begin(), v.end());
```

---

### Binary Search
```cpp
vector<int> v = {1, 2, 3, 4, 5, 6};  // must be sorted

// Binary search (returns bool)
binary_search(v.begin(), v.end(), 3);  // true

// Lower bound (first element >= x)
auto it = lower_bound(v.begin(), v.end(), 3);
int idx = it - v.begin();  // index
int val = *it;             // value

// Upper bound (first element > x)
auto it = upper_bound(v.begin(), v.end(), 3);

// For descending sorted array
vector<int> v = {6, 5, 4, 3, 2, 1};
auto it = lower_bound(v.begin(), v.end(), 3, greater<int>());
auto it = upper_bound(v.begin(), v.end(), 3, greater<int>());

// Custom comparator for pairs
vector<pair<int,string>> vp;
pair<int,string> ref = {timestamp, ""};
auto lambda = [](const pair<int,string> &p1, const pair<int,string> &p2) {
    return p1.first < p2.first;
};
auto it = upper_bound(vp.begin(), vp.end(), ref, lambda);

// For set/map (use member functions)
set<int> s;
s.lower_bound(x);  // O(log n)
s.upper_bound(x);  // O(log n)

map<int,int> mp;
mp.lower_bound(x);
mp.upper_bound(x);
```

---

### Min/Max Operations
```cpp
// Min/Max of two elements
min(a, b);
max(a, b);
min({a, b, c, d});  // initializer list
max({a, b, c, d});

// Min/Max element in container
vector<int> v = {3, 1, 4, 1, 5};
int minVal = *min_element(v.begin(), v.end());  // O(n)
int maxVal = *max_element(v.begin(), v.end());  // O(n)

// Get both min and max
auto [minIt, maxIt] = minmax_element(v.begin(), v.end());
int minVal = *minIt;
int maxVal = *maxIt;

// Custom comparator
auto minIt = min_element(v.begin(), v.end(), [](int a, int b) {
    return abs(a) < abs(b);  // min by absolute value
});
```

---

### Permutations
```cpp
vector<int> v = {1, 2, 3};

// Next permutation (lexicographically next)
next_permutation(v.begin(), v.end());  // {1, 3, 2}

// Previous permutation
prev_permutation(v.begin(), v.end());

// Generate all permutations
sort(v.begin(), v.end());  // start from smallest
do {
    // process permutation
} while(next_permutation(v.begin(), v.end()));

// Check if permutation exists
if(next_permutation(v.begin(), v.end())) {
    // permutation exists
}
```

---

### Rotation
```cpp
vector<int> v = {1, 2, 3, 4, 5};
int n = v.size();
int k = 2;

// Left rotate by k
rotate(v.begin(), v.begin()+k, v.end());
// Result: {3, 4, 5, 1, 2}

// Right rotate by k
rotate(v.begin(), v.begin()+(n-k), v.end());
// Result: {4, 5, 1, 2, 3}

// Check if string rotation
string s = "abcde";
string t = "cdeab";
bool isRotation = (s.length() == t.length() && 
                   (s+s).find(t) != string::npos);
```

---

### Reverse
```cpp
vector<int> v = {1, 2, 3, 4, 5};

// Reverse entire container
reverse(v.begin(), v.end());

// Reverse part of container
reverse(v.begin(), v.begin()+3);

// Reverse string
string s = "hello";
reverse(s.begin(), s.end());  // "olleh"
```

---

### Unique (Remove Duplicates)
```cpp
vector<int> v = {1, 1, 2, 2, 3, 3, 3};

// MUST sort first
sort(v.begin(), v.end());

// Remove consecutive duplicates
auto it = unique(v.begin(), v.end());
v.erase(it, v.end());  // actually remove them

// One-liner
v.erase(unique(v.begin(), v.end()), v.end());
```

---

### Partition
```cpp
vector<int> v = {1, 2, 3, 4, 5, 6};

// Partition (reorder so that predicate is true for first part)
auto it = partition(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;  // even numbers first
});

// Stable partition (maintains relative order)
stable_partition(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});

// Check if partitioned
is_partitioned(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});
```

---

### Count
```cpp
vector<int> v = {1, 2, 2, 3, 2, 4};

// Count occurrences
int cnt = count(v.begin(), v.end(), 2);  // 3

// Count with condition
int cnt = count_if(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;  // count even numbers
});
```

---

### Find
```cpp
vector<int> v = {1, 2, 3, 4, 5};

// Find element
auto it = find(v.begin(), v.end(), 3);
if(it != v.end()) {
    int idx = it - v.begin();  // index
}

// Find with condition
auto it = find_if(v.begin(), v.end(), [](int x) {
    return x > 3;
});

// Find if NOT
auto it = find_if_not(v.begin(), v.end(), [](int x) {
    return x < 3;
});
```

---

### Copy
```cpp
vector<int> src = {1, 2, 3, 4, 5};
vector<int> dest(5);

// Copy all
copy(src.begin(), src.end(), dest.begin());

// Copy with condition
vector<int> dest;
copy_if(src.begin(), src.end(), back_inserter(dest), [](int x) {
    return x % 2 == 0;  // copy only even numbers
});

// Copy n elements
copy_n(src.begin(), 3, dest.begin());
```

---

### Fill
```cpp
vector<int> v(5);

// Fill with value
fill(v.begin(), v.end(), 10);  // all elements = 10

// Fill n elements
fill_n(v.begin(), 3, 5);  // first 3 elements = 5

// Generate with function
int n = 0;
generate(v.begin(), v.end(), [&n]() { return n++; });
// v = {0, 1, 2, 3, 4}
```

---

### Replace
```cpp
vector<int> v = {1, 2, 3, 2, 4};

// Replace value
replace(v.begin(), v.end(), 2, 99);  // all 2s become 99

// Replace with condition
replace_if(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
}, 0);  // all even numbers become 0
```

---

### Remove
```cpp
vector<int> v = {1, 2, 3, 2, 4};

// Remove value (doesn't actually erase, just moves to end)
auto it = remove(v.begin(), v.end(), 2);
v.erase(it, v.end());  // actually erase

// Remove with condition
auto it = remove_if(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});
v.erase(it, v.end());
```

---

### Set Operations (on sorted ranges)
```cpp
vector<int> v1 = {1, 2, 3, 4, 5};
vector<int> v2 = {3, 4, 5, 6, 7};
vector<int> result;

// Union
set_union(v1.begin(), v1.end(), v2.begin(), v2.end(), 
          back_inserter(result));
// {1, 2, 3, 4, 5, 6, 7}

// Intersection
set_intersection(v1.begin(), v1.end(), v2.begin(), v2.end(), 
                 back_inserter(result));
// {3, 4, 5}

// Difference (elements in v1 but not in v2)
set_difference(v1.begin(), v1.end(), v2.begin(), v2.end(), 
               back_inserter(result));
// {1, 2}

// Symmetric difference (elements in either but not both)
set_symmetric_difference(v1.begin(), v1.end(), v2.begin(), v2.end(), 
                        back_inserter(result));
// {1, 2, 6, 7}

// For sets
set<int> s1, s2;
vector<int> result;
set_difference(s1.begin(), s1.end(), s2.begin(), s2.end(), 
               back_inserter(result));
```

---

### Transform
```cpp
vector<int> v = {1, 2, 3, 4, 5};
vector<int> result(5);

// Transform each element
transform(v.begin(), v.end(), result.begin(), [](int x) {
    return x * x;  // square each element
});

// Transform two ranges
vector<int> v1 = {1, 2, 3};
vector<int> v2 = {4, 5, 6};
vector<int> result(3);
transform(v1.begin(), v1.end(), v2.begin(), result.begin(), 
          [](int a, int b) {
    return a + b;
});

// String to lowercase
string s = "HELLO";
transform(s.begin(), s.end(), s.begin(), ::tolower);

// String to uppercase
transform(s.begin(), s.end(), s.begin(), ::toupper);
```

---

### All/Any/None
```cpp
vector<int> v = {2, 4, 6, 8};

// All elements satisfy condition
bool allEven = all_of(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});  // true

// Any element satisfies condition
bool anyOdd = any_of(v.begin(), v.end(), [](int x) {
    return x % 2 == 1;
});  // false

// No element satisfies condition
bool noneOdd = none_of(v.begin(), v.end(), [](int x) {
    return x % 2 == 1;
});  // true
```

---

## Utility Functions

### std::move
```cpp
// Transfer ownership without copying
string source = "hello";
string target = std::move(source);
// source is now empty, target has "hello"

vector<int> v1 = {1, 2, 3};
vector<int> v2 = std::move(v1);
// v1 is now empty, v2 has {1, 2, 3}

// Useful when pushing into containers
vector<string> vec;
string str = "example";
vec.push_back(std::move(str));  // no copy, str becomes empty

// With vectors
vector<int> temp = {1, 2, 3};
vector<vector<int>> result;
result.push_back(std::move(temp));  // no copy, temp becomes empty
```

---

### swap
```cpp
int a = 5, b = 10;
swap(a, b);  // a = 10, b = 5

vector<int> v1 = {1, 2, 3};
vector<int> v2 = {4, 5, 6};
swap(v1, v2);  // O(1) for containers
```

---

### back_inserter
```cpp
// Used with algorithms to insert at end
vector<int> v;
copy_if(src.begin(), src.end(), back_inserter(v), [](int x) {
    return x % 2 == 0;
});

// Without back_inserter, you'd need to pre-allocate
vector<int> v(size);
copy_if(src.begin(), src.end(), v.begin(), [](int x) {
    return x % 2 == 0;
});
```

---

## String Operations

### Basic Operations
```cpp
string s = "hello";

// Length
s.length();
s.size();

// Access
s[i];
s.at(i);
s.front();
s.back();

// Modify
s.push_back('!');
s.pop_back();
s += " world";
s.append(" world");
s.insert(pos, "text");
s.erase(pos, len);
s.clear();

// Substring
s.substr(pos);       // from pos to end
s.substr(pos, len);  // from pos, length len

// Find
size_t pos = s.find("ll");        // first occurrence
size_t pos = s.find("ll", pos);   // from position pos
size_t pos = s.rfind("ll");       // last occurrence
if(pos != string::npos) { }       // found

// Replace
s.replace(pos, len, "new");

// Compare
s.compare(t);  // returns 0 if equal, <0 if s<t, >0 if s>t
s == t;        // easier comparison
```

---

### String Conversion
```cpp
// String to number
string s = "12345";
int x = stoi(s);           // string to int
long l = stol(s);          // string to long
long long ll = stoll(s);   // string to long long
float f = stof(s);         // string to float
double d = stod(s);        // string to double

// Number to string
int x = 12345;
string s = to_string(x);

// Character to int
char c = '5';
int x = c - '0';  // 5

// Int to character
int x = 5;
char c = x + '0';  // '5'
```

---

### stringstream
```cpp
#include <sstream>

// String to number
string s = "12345";
stringstream ss(s);
int x;
ss >> x;  // x = 12345

// Count words
string sentence = "hello world foo bar";
stringstream ss(sentence);
string word;
int count = 0;
while(ss >> word) count++;

// Extract numbers from string
string complex = "1+2i";
stringstream ss(complex);
int real, imag;
char skip;
ss >> real >> skip >> imag >> skip;
// real = 1, imag = 2

// Parse CSV
string csv = "1,2,3,4";
stringstream ss(csv);
string token;
while(getline(ss, token, ',')) {
    // process token
}
```

---

### Regex
```cpp
#include <regex>

// Replace all vowels with empty string
string s = "hello";
regex rgx("[aeiouAEIOU]");
string result = regex_replace(s, rgx, "");  // "hll"

// Replace dots with [.]
string ip = "1.2.3.4";
regex rgx("\\.");
string result = regex_replace(ip, rgx, "[.]");  // "1[.]2[.]3[.]4"

// Match pattern
regex rgx("\\d+");  // one or more digits
if(regex_search(s, rgx)) { }

// Find all matches
string s = "123 abc 456";
regex rgx("\\d+");
sregex_iterator it(s.begin(), s.end(), rgx);
sregex_iterator end;
while(it != end) {
    cout << it->str() << endl;  // 123, 456
    ++it;
}
```

---

## Numeric Operations

### accumulate
```cpp
#include <numeric>

vector<int> v = {1, 2, 3, 4, 5};

// Sum
int sum = accumulate(v.begin(), v.end(), 0);  // 15

// Product
int product = accumulate(v.begin(), v.end(), 1, multiplies<int>());

// Custom operation
int sumOfSquares = accumulate(v.begin(), v.end(), 0, [](int sum, int x) {
    return sum + x*x;
});

// 2D matrix sum
vector<vector<int>> matrix;
int total = accumulate(matrix.begin(), matrix.end(), 0, 
    [](int sum, vector<int> &row) {
        return sum + accumulate(row.begin(), row.end(), 0);
    });
```

---

### iota
```cpp
#include <numeric>

vector<int> v(5);
iota(v.begin(), v.end(), 0);  // {0, 1, 2, 3, 4}
iota(v.begin(), v.end(), 10); // {10, 11, 12, 13, 14}
```

---

### gcd and lcm (C++17)
```cpp
#include <numeric>

int g = gcd(12, 18);   // 6
int l = lcm(12, 18);   // 36
```

---

### partial_sum
```cpp
#include <numeric>

vector<int> v = {1, 2, 3, 4, 5};
vector<int> prefix(5);

partial_sum(v.begin(), v.end(), prefix.begin());
// prefix = {1, 3, 6, 10, 15}
```

---

### adjacent_difference
```cpp
#include <numeric>

vector<int> v = {1, 3, 6, 10, 15};
vector<int> diff(5);

adjacent_difference(v.begin(), v.end(), diff.begin());
// diff = {1, 2, 3, 4, 5}
```

---

### hypot (Hypotenuse)
```cpp
#include <cmath>

// 2D distance
double dist = hypot(3, 4);  // 5.0

// Distance between two points
double dist = hypot(x2-x1, y2-y1);

// 3D distance (C++17)
double dist3D = hypot(x, y, z);

// Safer than sqrt(x*x + y*y) - avoids overflow
```

---

## Advanced Techniques

### Custom Comparators
```cpp
// For sorting
vector<int> v;
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;  // descending
});

// For pairs - sort by second, then first
vector<pair<int,int>> vp;
sort(vp.begin(), vp.end(), [](auto &a, auto &b) {
    if(a.second == b.second) return a.first < b.first;
    return a.second < b.second;
});

// For custom objects
struct Person {
    string name;
    int age;
};
vector<Person> people;
sort(people.begin(), people.end(), [](Person &a, Person &b) {
    return a.age < b.age;
});
```

---

### Lambda Functions
```cpp
// Basic lambda
auto add = [](int a, int b) { return a + b; };
int result = add(3, 5);  // 8

// Capture by value
int x = 10;
auto lambda = [x](int a) { return a + x; };

// Capture by reference
int x = 10;
auto lambda = [&x](int a) { x += a; };

// Capture all by value
auto lambda = [=](int a) { return a + x + y; };

// Capture all by reference
auto lambda = [&](int a) { x += a; y += a; };

// Mixed capture
auto lambda = [x, &y](int a) { y = x + a; };

// Mutable lambda (modify captured values)
int x = 10;
auto lambda = [x]() mutable { x++; return x; };
```

---

### Bit Manipulation with STL
```cpp
#include <bitset>

bitset<8> b1("10101010");
bitset<8> b2(170);  // same as above

// Operations
b1.set();       // set all bits to 1
b1.reset();     // set all bits to 0
b1.flip();      // flip all bits
b1.set(i);      // set bit i to 1
b1.reset(i);    // set bit i to 0
b1.flip(i);     // flip bit i
b1.test(i);     // check if bit i is 1
b1[i];          // access bit i

// Count
b1.count();     // number of 1s
b1.size();      // total bits
b1.any();       // any bit is 1
b1.none();      // no bit is 1
b1.all();       // all bits are 1

// Conversion
unsigned long x = b1.to_ulong();
string s = b1.to_string();

// Built-in functions
__builtin_popcount(x);      // count 1s in int
__builtin_popcountll(x);    // count 1s in long long
__builtin_clz(x);           // count leading zeros
__builtin_ctz(x);           // count trailing zeros
__builtin_ffs(x);           // find first set bit (1-indexed)
```

---

### Policy-Based Data Structures
```cpp
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
using namespace __gnu_pbds;

// Ordered set (supports order statistics)
typedef tree<int, null_type, less<int>, rb_tree_tag,
             tree_order_statistics_node_update> ordered_set;

ordered_set os;
os.insert(5);
os.insert(3);
os.insert(7);

// Find by order (0-indexed)
auto it = os.find_by_order(1);  // iterator to 2nd smallest (5)

// Order of key (number of elements strictly less than k)
int cnt = os.order_of_key(6);   // 2 (elements 3 and 5 are < 6)

// All set operations work
os.erase(5);
os.find(3);
os.size();
```

---

### Debugging Tricks
```cpp
// Print vector
template<typename T>
void print(vector<T> &v) {
    for(auto &x : v) cout << x << " ";
    cout << endl;
}

// Print 2D vector
template<typename T>
void print2D(vector<vector<T>> &v) {
    for(auto &row : v) {
        for(auto &x : row) cout << x << " ";
        cout << endl;
    }
}

// Print map
template<typename K, typename V>
void printMap(map<K,V> &mp) {
    for(auto &[k, v] : mp) {
        cout << k << " -> " << v << endl;
    }
}

// Assert with message
#define assertm(exp, msg) assert(((void)msg, exp))
```

---

## Complexity Reference

### Containers

| Container | Access | Insert/Delete (end) | Insert/Delete (middle) | Search | Space |
|-----------|--------|---------------------|------------------------|--------|-------|
| vector | O(1) | O(1) amortized | O(n) | O(n) | O(n) |
| deque | O(1) | O(1) | O(n) | O(n) | O(n) |
| list | O(n) | O(1) | O(1)* | O(n) | O(n) |
| set/map | O(log n) | O(log n) | O(log n) | O(log n) | O(n) |
| unordered_set/map | O(1) avg | O(1) avg | O(1) avg | O(1) avg | O(n) |
| priority_queue | O(1) top | O(log n) | - | - | O(n) |
| stack/queue | O(1) | O(1) | - | - | O(n) |

*if you have iterator to position

### Algorithms

| Algorithm | Complexity |
|-----------|------------|
| sort | O(n log n) |
| stable_sort | O(n log n) |
| partial_sort | O(n log k) |
| binary_search | O(log n) |
| lower_bound/upper_bound | O(log n) |
| min_element/max_element | O(n) |
| reverse | O(n) |
| rotate | O(n) |
| unique | O(n) |
| next_permutation | O(n) |
| partition | O(n) |
| nth_element | O(n) avg |

---

## Common Patterns

### Two Pointers
```cpp
// Remove duplicates from sorted array
int i = 0;
for(int j = 1; j < n; j++) {
    if(v[j] != v[i]) {
        v[++i] = v[j];
    }
}
```

### Sliding Window
```cpp
// Maximum sum subarray of size k
int maxSum = 0, windowSum = 0;
for(int i = 0; i < k; i++) windowSum += arr[i];
maxSum = windowSum;

for(int i = k; i < n; i++) {
    windowSum += arr[i] - arr[i-k];
    maxSum = max(maxSum, windowSum);
}
```

### Prefix Sum
```cpp
vector<int> prefix(n+1, 0);
for(int i = 0; i < n; i++) {
    prefix[i+1] = prefix[i] + arr[i];
}
// Sum of range [l, r]
int rangeSum = prefix[r+1] - prefix[l];
```

### Monotonic Stack
```cpp
// Next greater element
vector<int> nextGreater(n, -1);
stack<int> st;
for(int i = n-1; i >= 0; i--) {
    while(!st.empty() && st.top() <= arr[i]) {
        st.pop();
    }
    if(!st.empty()) nextGreater[i] = st.top();
    st.push(arr[i]);
}
```

### Union Find (DSU)
```cpp
class DSU {
    vector<int> parent, rank;
public:
    DSU(int n) : parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }
    
    int find(int x) {
        if(parent[x] != x) {
            parent[x] = find(parent[x]);  // path compression
        }
        return parent[x];
    }
    
    void unite(int x, int y) {
        int px = find(x), py = find(y);
        if(px == py) return;
        if(rank[px] < rank[py]) swap(px, py);
        parent[py] = px;
        if(rank[px] == rank[py]) rank[px]++;
    }
};
```

---

## Quick Tips

1. **Use `emplace_back` instead of `push_back`** for better performance (constructs in-place)
   ```cpp
   v.emplace_back(1, 2);  // for pair<int,int>
   ```

2. **Reserve capacity** for vectors when size is known
   ```cpp
   v.reserve(n);
   ```

3. **Use `unordered_map/set`** when order doesn't matter (O(1) vs O(log n))

4. **Use `auto`** to avoid typing long types
   ```cpp
   auto it = mp.find(key);
   ```

5. **Structured bindings** (C++17) for cleaner code
   ```cpp
   for(auto [key, val] : mp) { }
   ```

6. **Use `const auto&`** in range-based loops to avoid copies
   ```cpp
   for(const auto &x : v) { }
   ```

7. **`__gcd(a, b)`** is available without including anything

8. **Fast I/O** for competitive programming
   ```cpp
   ios_base::sync_with_stdio(false);
   cin.tie(NULL);
   ```

9. **Use `'\n'` instead of `endl`** (faster, doesn't flush)

10. **Lambda in sort** for quick custom comparisons
    ```cpp
    sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
    ```

---

## Common Mistakes to Avoid

1. ❌ Forgetting to sort before `unique()`
2. ❌ Using `v[i]` on empty vector (use `push_back` or resize first)
3. ❌ Not checking `find()` result against `end()`
4. ❌ Modifying container while iterating (use erase-remove idiom)
5. ❌ Integer overflow (use `long long` when needed)
6. ❌ Comparing `size()` with negative numbers (size_t is unsigned)
7. ❌ Not handling edge cases (empty input, single element, etc.)
8. ❌ Using `map[key]` for checking existence (creates entry if not exists, use `find()`)

---

## Resources for Practice

- **LeetCode**: Practice problems by topic
- **Codeforces**: Competitive programming contests
- **AtCoder**: High-quality problems
- **CSES Problem Set**: Comprehensive problem collection
- **CP-Algorithms**: Detailed algorithm explanations

---

**Happy Coding! 🚀**
