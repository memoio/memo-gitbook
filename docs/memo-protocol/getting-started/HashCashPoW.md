# HashCash and PoW in Grid

## About HashCash

Let's consider a question: How to write a program that can consume the other party's time?

Consuming time is not difficult, just take a nap:

Sleep(1000)
This indeed consumes time, but it does not consume CPU. If the other party uses a speed changer, this can be completed in an instant.

However, to consume CPU is not difficult either, just write a large loop:

plaintext
for i = 0 to 1000000000
end
But this is essentially no different from Sleep. How do we know if the other party has actually run it?

Therefore, we need a result to return — only a complete run will have the correct answer.

plaintext
result = 0
for i = 0 to 1000000000
    result = result + i
end
return result
By returning a result, we can verify whether the other party has fully run our program.

However, the above problem is, after all, too simple. Even elementary school students know that they can directly calculate the result using a series formula, without having to spend time running the loop.

What kind of function cannot be speculated by a formula? In other words, it is necessary to run the function honestly to get the result. And there is no faster algorithm that can also produce the same result.

Obviously, a 'one-way hash function' is. For example, a classic problem:

MD5(X) == X
cannot be solved by a formula. To find the answer, one can only try one by one, which requires a lot of time.

But for the verifier, it is very easy — just calculate the received answer once to judge right or wrong.

Hashcash
Of course, the above example is too difficult, and an answer cannot be found in a short time. But some improvements can be made, such as only requiring the result to have a few leading zeros:

Hash(X) == "0000......"
The fewer the number of zeros, the easier it is to find the X that meets the condition.

At the same time, to prevent the answer from being reused, a salt value can be added:

Hash(X, Salt) == "0000......"
The salt can be provided by the verifier, which can act as a 'question'. The X that meets the condition can be regarded as the 'answer' to the question, submitted to the verifier for verification.

This is the so-called PoW (Proof-of-Work), a mechanism to verify whether the other party has invested computational work. And only a small amount of resources are needed to verify a large amount of work.

PoW implemented with hash functions is called Hashcash. In reality, Bitcoin uses a similar principle, using SHA-256 as the hash function. With authoritative cryptographic algorithms as a guarantee, it can only be brute-forced, and results cannot be obtained by cunning methods, ensuring the value of mining work.

## PoW in Grid

For Grid project, we use similiar tech as hashcash to do the PoW task.

Here is the core function used to calculate hash result of a specified difficult and random value:

```c
void runSha256(int diffcult, int index, BYTE *data, size_t len, long long *result, long long n)
{
    int blockSize = 16;
    int numBlocks = (n + blockSize - 1) / blockSize;

    sha256_cuda<<<numBlocks, blockSize>>>(diffcult, index, data, len, result, n);
}
```

A result is computed with given conditions: difficult and random data, and output with result paramter.

The difficult and data is provided by the validator for every computing node in Grid, and the computing node must try to get the correct result of each challenge, send this result to the validator for verification to check if this challenge passed.
