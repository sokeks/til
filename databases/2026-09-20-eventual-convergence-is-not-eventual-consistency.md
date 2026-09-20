# TIL: Can strictly consistent database be eventual consistent?

## What issue did I encounter?

I've always said that, that *Designing-Data Intense Applications* by Martin Kleppman, is a bad book for non-experts and I still don't know why so many sources provide it in an answer for a question of *what should I read to learn System Design?* Definitely not that one, even though author is very smart and provide a solid dose of knowledge. But the reading is so complex, so theoretical, so not straight-forward, that if you're new to the topic, you'll get lost. Leave it, it's my honest advice. Recently however I was reading it for a second time (still not a great fan), and I was hit with an interesting dilemma:

If a database provides **Strict (or Strong) Consistency** guarantee, can it use **asynchronous replication** and allow direct reads from replicas? 


## My previous mental model

My previous view was, that:
1. **Asynchronous replication** means the leader sends the written data to some replicas without waiting for their confirmation before ACKing the write.
2. **Read from replica** means clients can read directly from replica without using leader (quite a standard configuration in read-heavy systems).

If so, we may always get to the situation as below, and due to uncertainty about what Replica B will be serving to Client 3, we would call this situation **Eventually Consistent** (saying that the data will eventually converge, but meantime client may read the stale data).

<img width="789" height="423" alt="image" src="https://github.com/user-attachments/assets/5117c0e6-71cc-4932-8951-efcb9f561b4d" />

So to be sure to get the **Strict Consistency**, we would need to have synchronous replication as below, correct?

<img width="789" height="397" alt="image" src="https://github.com/user-attachments/assets/926f4e23-eeda-48cc-96a9-c4c8e4094461" />



## What I learned

But what would happen if we move to leaderless system and allow replicas to consult the data with other instances in the system (basically how it's done in leaderless Cassandra or DynamoDB), and allow to verify the latest value? In that situation we get this:

<img width="115" height="119" alt="image" src="https://github.com/user-attachments/assets/e0bdf4d1-ee73-4649-b946-42b8f232e414" />

Is that now a **Strict Consistency**?

Honestly, I couldn't find a straightforward answer. There is not a central definition and one reference to refer to. Of course, some will say, that in that example we don't deal with **Strict Consistency**, but **Linearizability** (so that a distributed memory is viewed by the external client, as if it was a single piece of memory) and the presented behavior is **eventual convergence**. Honestly I'd even tend to agree with this. BUT the case is: will everyone agree? Or even if everyone agrees, won't anyone make a mistake and call it so?

That's why, what **Today I learned** is: in computer science do not stick too strictly to definitions and when you find such one, make sure, you really know, what it means, and when you write a Design Doc and use the term, make sure you provide a good glossary with its explanation.

---
