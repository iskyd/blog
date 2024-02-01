---
layout: post
title: Learning Zig and Bitcoin - Part 2 (BIP 38)
---

#### BIP 38

BIP 38 propose a method for encrypting and encoding a passphrase protected Bitcoin private key. This method use scrypt to resist brute force attack.
This method propose two encoding metodologies, but I'm going to focus only on the one that permit any private key to be encrypted with any passphrase without using the EC multiplication.
You can read more about [BIP 38 here](https://en.bitcoin.it/wiki/BIP_0038) in particular you can find the steps of what I've implemented [here](https://en.bitcoin.it/wiki/BIP_0038#Encryption_when_EC_multiply_flag_is_not_used)


#### My implementation

As you can see the steps to encrypt and decrypt a private key without EC multiplication are straighforward, but in Zig...
This implementation forces me to dive deep in the crypto code in the std lib and well, it's a crap. I've started looking in the crypto lib when using secp256k1 (prev article), but I didn't find a way to get it working so I [wrote my implementation of secp256k1](https://github.com/iskyd/walle/blob/main/src/secp256k1/secp256k1.zig).
This time I've worked with aes and scrypt in order to implement bip38 specs. The code of both lib is not clear and tests are not complete. I want also to point out that at the time of writing the crypto code inside the zig std lib has not been audited by a security expert. [There will be an audit before 1.0 release](
https://github.com/ziglang/zig/issues/5763).
You can find my implementation of Bip 38 [here](https://github.com/iskyd/walle/blob/main/src/bip38/bip38.zig). As of today there is a big major issue in my code: passphrase is not passed as bytes but as a string to the scrypt algorithm. This is due to the fact that trying to convert a string to bytes using std.mem.asBytes results in some sort of random behaviour that I have not yet checked.

#### Next steps

I need to fix the Bip 38 implementation using the passphrase as bytes and try to compare results with other implementation to garantuee the usability.
Next I'm going to implement transactions and transaction signature, in particular I want to implement a multisig transaction where m of n keys are required to move the funds.
Also I'm going to try to make walle usable via cli so that anyone can try it.

#### Conclusion

After few months working with Zig I can say that I'm enjoying it very much and I can't think of getting back from it. On the other side Zig seems far from releasing a 1.0 version and it lacks lot of feature/tests in particular in the crypto lib (the one that is impacting the most my project).

During this days [Zig Software Foundation announced a fundraise](https://ziglang.org/news/2024-financials/#2024-financial-report-and-fundraiser), so if you like Zig consider donating to the ZSF so that we can get closer to the 1.0.

Also [SYCL](https://sycl.it/) annouced a new event in Milan, see you there :)
