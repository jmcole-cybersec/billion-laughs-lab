Billion Laughs (XML bomb / Exponential Entity Expansion)
======================================================
A hands on lab demonstrating the Billion Laughs denial of service attack against an XML parser.

Every machine in this lab is owned by me and runs on an isolated virtual network.

I built a vulnerable XML parser service to attack with Billion Laughs.
Modern lxml blocks this by default, so I had to downgrade to lxml version 4.9.1 on python 3.10 in order for the attack to work.
The bomb maxed out the CPU and memory for a few minutes.

What this project demonstrates
- Reconnaissance of a target service with Nmap (host discovery + port scanning)
- Understanding and use of an XML amplification attack
- Exploring modern parser's built in defense compared to vulnerable legacy versions


How Billion Laughs works
===========================

XML DTDs allow entities, which are text macros where '\&name;' expands to a defined value.
The attack abuses recursive entities where each entity references the previous one 10 times.

-----------------------------------------------------------
<?xml version="1.0"?>
<!DOCTYPE lolz [
 <!ELEMENT lolz (#PCDATA)>
 <!ENTITY lol1 "lollollollollollollollollollol">
 <!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
 <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
......
]>
<lolz>&lol9;</lolz>
-----------------------------------------------------------
Reference: https://en.wikipedia.org/wiki/Billion_laughs_attack

With nine levels this results in 10^9 expansions (a billion), all generated from a <1 KB file.

Classification: CWE-776
Reference: https://cwe.mitre.org/data/definitions/776.html

I find this type of attack fascinating, because unlike typical volumetric DDoS attacks, this
attack comes with a huge discrepancy between the payload and materialized attack.


Lab Setup
=========
- Attacker: Laptop (Windows, Python 3.14, Nmap, requests)
- Target: Windows VM (Python 3.10, Flask, lxml 4.9.1)
- Network: Isolated virtual network (host-only)

Remediation checklist: 
==============================
-Disable DTD and entity resolution on untrusted XML
-Use a hardened parser (ex: defusedxml in Python)
-Enforce amplification/expansion limit if entities are allowed
-Apply size limits so oversized payloads are rejected before parsing


Related attacks I will explore later:
==========================================================
-XXE (XML External Entity)
-YAML bombs
-Zip/decompression bombs


