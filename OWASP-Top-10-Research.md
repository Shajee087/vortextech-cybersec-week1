# Researching the OWASP Top 10 Vulnerabilities

**Vortex Tech Cyber Security Internship Track — Week 1 (Beginner)**
**Intern:** Shajee Mushtaque
**Task:** Task 1: Research the OWASP Top 10 Vulnerabilities


## A quick note on the list I used

OWASP released an updated Top 10 (the **2025 edition**) which reshuffled several rankings and added two brand-new categories. I've used the current 2025 names and rankings below instead of the older 2021 version, since that's what's actually live on owasp.org right now.


## 1. Broken Access Control (#1)

**What it is:**
Access control is basically the set of rules that decide what a logged-in user is and isn't allowed to see or do. "Broken" access control means those rules aren't enforced properly so a regular user can end up doing things only an admin should be able to do, or viewing someone else's private data just by asking for it the right way.

**How an attacker exploits it:**
One of the simplest versions is called an Insecure Direct Object Reference (IDOR). Imagine a website shows your invoice at a link like `site.com/invoice?id=1005`. If the server doesn't check whether invoice #1006 actually belongs to you before showing it, an attacker can just change the number in the URL and read other people's invoices no hacking skill required, just curiosity and a browser address bar.

**Real-world example:**
In 2019, title insurance company First American Financial exposed roughly 885 million sensitive real estate documents bank account numbers, Social Security numbers, mortgage records, driver's license images going back 16 years. The documents were reachable through sequentially numbered links with no login required, so anyone who knew (or guessed) a document number could open someone else's file. A security researcher testing his own document link discovered he could just change one digit and see a completely different customer's records.

**Prevention:**
Every request for a resource (a document, an account, an order) should be checked server-side to confirm the logged-in user actually owns or has permission to view that specific item — not just that they're logged in at all. Using random, non-sequential IDs instead of predictable numbers also makes guessing much harder.


## 2. Security Misconfiguration (#2)

**What it is:**
This isn't really a coding flaw it's a setup mistake. It happens when a server, cloud service, database, or application is left with default settings, unnecessary features turned on, overly permissive access, or a security control that just wasn't configured correctly.

**How an attacker exploits it:**
Attackers actively scan the internet looking for these mistakes an open cloud storage bucket, a firewall rule that's more permissive than it should be, a debug panel left enabled in production. Once they find one weak setting, they use it as a doorway into systems that would otherwise be well protected.

**Real-world example:**
In 2019, Capital One suffered a breach affecting over 100 million customers. The root cause was a misconfigured web application firewall in their AWS cloud setup. A former AWS employee found the misconfiguration and used a technique that tricked the firewall into forwarding requests to an internal AWS metadata service which handed back temporary credentials. Those credentials were then used to access more than 700 storage buckets full of customer data. No malware, no phishing just one wrong setting.

**Prevention:**
Regularly audit cloud and server configurations against a security checklist (disable unused services, remove default credentials, restrict firewall rules to the minimum needed). Automated configuration-scanning tools can catch drift before an attacker does.


## 3. Cryptographic Failures (#4)

**What it is:**
This covers anything related to protecting sensitive data with weak, missing, or incorrectly implemented encryption for example storing passwords in a way that can be reversed back to plain text, or using outdated encryption algorithms.

**How an attacker exploits it:**
If sensitive data is stolen but properly protected (e.g., passwords run through a modern one-way hashing algorithm), the attacker gets a pile of data they still can't easily use. But if the protection is weak or reversible, stealing the database is often enough to recover the original information directly.

**Real-world example:**
In Adobe's 2013 breach, attackers stole data for roughly 150 million accounts. The big mistake wasn't just that the data was stolen it's that Adobe had encrypted passwords using an old, reversible method (Triple DES in ECB mode) instead of hashing them with a proper one-way algorithm. Because of how that encryption mode works, identical passwords produced identical encrypted output, and Adobe had also stored users' plain-text password hints alongside them. Researchers were able to figure out the most common passwords (like "123456") just by counting how often the same encrypted value repeated and cross-referencing the hints.

**Prevention:**
Sensitive data like passwords should never be stored using reversible encryption. Use a proper one-way password hashing algorithm designed for this purpose (like bcrypt or Argon2), combined with a unique salt per user, so even identical passwords produce different stored values.


## 4. Injection SQL Injection (#5)

**What it is:**
Injection happens when an application takes user input and inserts it directly into a command (often a database query) without properly checking or cleaning it first. This lets an attacker "inject" their own instructions into that command.

**How an attacker exploits it:**
Say a login form builds a database query like: `SELECT * FROM users WHERE username = 'INPUT'`. If the app just drops whatever the user types into that string, an attacker can type something like `' OR '1'='1` instead of a real username. The query becomes something that's always true, and the attacker can potentially log in without knowing a real password, or worse, pull entire tables of data out of the database.

**Real-world example:**
In October 2015, UK telecom provider TalkTalk was breached through SQL injection vulnerabilities in old webpages inherited from a company they'd acquired years earlier. Attackers used a common, publicly available SQL injection scanning tool to find the flaw and extracted personal data for nearly 157,000 customers, including bank account and sort code details for over 15,000 of them. What made it worse: two earlier SQL injection attempts against the same pages had gone unnoticed months before, because the pages weren't being properly monitored. TalkTalk was later fined and estimated the total cost of the incident at tens of millions of pounds.

**Prevention:**
Use parameterized queries (also called prepared statements) instead of building SQL commands by directly combining strings with user input. This treats user input strictly as data, never as executable code, which closes off this entire category of attack.

## 5. Authentication Failures (#7)

**What it is:**
This category covers weaknesses in how a system verifies that someone logging in really is who they claim to be things like allowing unlimited password guesses, weak password rules, or poor session handling.

**How an attacker exploits it:**
If a login system doesn't limit or slow down repeated failed login attempts, an attacker can write a script that tries thousands of password guesses per minute against an account. Since a lot of people reuse common or weak passwords, this "brute-forcing" eventually succeeds especially if there's no lockout, no CAPTCHA, and no alert triggered by the failed attempts.

**Real-world example:**
In 2014, a wave of private celebrity photos was leaked from iCloud accounts in an incident often referred to as "Celebgate." Investigation showed that alongside targeted phishing, attackers had used a tool nicknamed "iBrute," which exploited the fact that Apple's "Find My iPhone" service didn't limit or lock out repeated failed password attempts. This let attackers try huge numbers of passwords against an account without triggering any warning, until a weak or reused password worked. Apple patched the flaw shortly after and pushed users toward two-factor authentication.

**Prevention:**
Rate-limit and lock out accounts after a reasonable number of failed login attempts, and require multi-factor authentication for anything sensitive. MFA means even a correctly guessed password isn't enough on its own to get in.


## Personal Reflection

Out of everything I researched, Security Misconfiguration surprised me the most. I expected the big breaches to come from clever, complex hacking, but Capital One's breach came down to a single wrong firewall setting, not some advanced exploit. It's a bit unsettling that something that basic can expose over 100 million people's data. It's also made me think differently about what "security" actually means in practice: a lot of it isn't about writing perfect code, it's about disciplined configuration, monitoring, and catching small mistakes before someone else finds them first.


*Vortex Tech · Internship Program 2026 · Week 1 Submission*
