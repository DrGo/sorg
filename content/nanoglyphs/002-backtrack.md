+++
published_at = 2019-07-19T16:57:21Z
title = "Backtrack Collapse"
+++

Okay, week two. As planned, the dark launch of week one was entirely unremarkable in every way. Despite the pontificating at length on time constraints, I wound up blowing through all the ones I'd set. Writing is hard; writing fluently -- getting words down on paper quickly -- is _really_ hard.

I've attended a few [NaNoWriMo][nanowrimo] meetups where it's common to "word race". Participants "sprint" by getting as many words down as possible in a fixed time window. The winner is the highest word count at the end. I tried one a few years ago, going in brimming with confidence -- surely with well-developed Vim muscles I'd run circles around these tool-deficient artists!

I came out proudly announcing my number, only to have that confidence turn to disbelief and then to despair. I hadn't just lost, I'd been _demolished_, and by every other racer. Not only that, but the winner had taken the gold using pen & paper.

![Iceland](/assets/images/nanoglyphs/002-backtrack/iceland@2x.jpg)

Today's photo is one that I took in Iceland in 2007. At the time I was shooting with a tiny handheld Casio EX-S600, quite possibly the best compact camera form factor of pre-smartphone days. _Many_ very amateur shots were taken (and if I'm honest, still are), but a lucky few beat the odds to turn out pretty well. Winning the Icelandic weather lottery with sun and (mostly) blue skies for an unlikely number of days that trip didn't hurt either.

Like nearly all modern photography, mine tend to vanish into the digital vaults of Dropbox, iCloud, or Google Photos. Old photos aren't gone, but they're lost in an hoard of 1s and 0s so vast that no one spends the time to go back and find them. Why not recycle a few here and there?

---

## An outage in seven characters (#seven-characters)

[Cloudflare's postmortem of their July 2nd outage][cloudflare] was the highlight of the week. Like most production incidents, there were a variety of contributing factors, but the most proximate cause was the introduction of a new regular expression. Specifically, this part of it: `.*.*=.*`.

Backtracking is a technique that some regex engines use to match as generously as possible, but which has the serious disadvantage of potentially being very expensive to do so, and with that expense growingly non-linearly with input size. Matching `x=x` to `.*.*=.*` takes 23 steps on a backtracking engine, `x=xx` takes 33, and `x=xxx` takes 45. Scroll down to the post's appendix for a deep explanation of the effect, including animated visualization.

The solution is complicated if you're developing a regex engine, but simple for most of us. Some engines like [RE2][re2] or [Rust's `regex`][rustregex] jettison a few features to optimize for speed and security, and guarantee execution in linear time with respect to expression and input size by avoiding backtracking. Regex constructs that require backtracking like backreferences or look-around assertions are explicitly not supported.

## Serverless Postgres (#serverless-postgres)

Amazon [GAed their serverless version of Postgres][aurorapostgres]. We've known for some time that Aurora's secret sauce is being able to plug a scalable storage layer into popular databases like MySQL and Postgres (up to 64 TB -- just try that on vanilla Postgres and watch it catch fire). What I hadn't realized is that they're apparently able to plug a specific user's store into a generically provisioned database server, and very quickly.

The announcement describes how serverless Aurora is fast enough to handle immediate workloads by maintaining a warm pool of standby servers provisioned and ready to go. When a request comes in for a database that's not running, they pull a server out of the standby fleet, plug in the database's storage, and it's ready to go. I'll withhold final judgements before trying it, but from a distance -- that's impressive.

I can't tell whether a serverless database is a good idea, but the idea is so novel that it's hard not to love. Within the next few years we should have a better understanding of whether the serverless craze really is the next deployment stack of the future, or a buzzword that's been popular for developer tools companies looking to make a buck to latch onto.

## Fad or fixture (#fad-or-fixture)

Fred's [reflections on ten years of Erlang][tenyears] is worth a read. My opinion these days is that between Erlang's subcritical mass, slow runtime, and abhorrent syntax, no one should be starting new projects on it, but it was unquestionably a source of a lot of good ideas, and some pieces of it like [OTP][otp] (blocks for building robust applications easily available as part of the language itself) were _so_ good that other languages should still be learning from them today.

---

Hopefully you're finding these somewhat useful. I'm certainly finding distilling information to be a useful learning exercise -- normally when reading a technical piece it's easy to have information flow into one side of the head and out the other.

Remember to hit the "reply" button if you have any feedback or ideas on what's here.

Otherwise, until next time.

[aurorapostgres]: https://aws.amazon.com/blogs/aws/amazon-aurora-postgresql-serverless-now-generally-available/
[cloudflare]: https://blog.cloudflare.com/details-of-the-cloudflare-outage-on-july-2-2019/
[nanowrimo]: https://en.wikipedia.org/wiki/National_Novel_Writing_Month
[otp]: https://learnyousomeerlang.com/what-is-otp
[re2]: https://github.com/google/re2/
[rustregex]: https://docs.rs/regex/1.1.9/regex/
[tenyears]: https://ferd.ca/ten-years-of-erlang.html
