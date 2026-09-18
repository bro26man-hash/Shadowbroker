# RESEARCH_NOTES.md — ShadowBroker OSINT Platform
## Podcast Research: Digital Rights & Surveillance Technology

> **Repository:** [BigBodyCobain/Shadowbroker](https://github.com/BigBodyCobain/Shadowbroker) → Forked to `bro26man-hash/Shadowbroker`
> **Stars:** 11,186 | **Forks:** 1,790 | **License:** AGPL-3.0 | **Language:** Python/JavaScript
> **Research compiled:** September 2026

---

## 1. PROJECT OVERVIEW

ShadowBroker is a decentralized geospatial intelligence platform that aggregates real-time telemetry from **60+ live intelligence feeds** into a single map interface. It tracks aircraft, ships, satellites, CCTV cameras, police scanners, mesh radio nodes, GPS jamming zones, internet-connected devices, conflict events, and more — all updates in real time.

Key capabilities that make it extraordinary (and extraordinary):

- **Aviation:** Tracks Air Force One, private jets of billionaires, military tankers, ISR aircraft — full flight trail accumulation, holding pattern detection
- **Maritime:** 25,000+ AIS vessels, fishing activity, billionaire superyachts, carrier strike group estimation
- **Surveillance:** **22,000+ live CCTV cameras** across 10 countries (London, NYC, California, Spain, Singapore, etc.)
- **Space:** Real-time satellite tracking with mission-type classification (military recon, SIGINT, SAR, etc.)
- **Conflict:** GDELT-powered global incident aggregation, Ukraine frontline, SIGINT news feeds
- **Infrastructure:** 35,000+ power plants, 2,000+ data centers, military bases, internet outage monitoring, submarine cable routes
- **RF/SIGINT:** 500+ KiwiSDR receivers, police/fire scanner feeds, GPS jamming detection, Meshtastic mesh radio
- **AI Agent Channel:** A bidirectional HMAC-signed command channel that lets AI agents (Claude, GPT, etc.) act as co-analysts with full read/write access to all data layers
- **InfoNet Testnet:** An experimental decentralized obfuscated messaging mesh with gate personas, dead drops, and a "Sovereign Shell" governance economy

The project's own framing: *"The knowledge is available to all but rarely aggregated in the open, until now."*

---

## 2. WHY THIS PROJECT MATTERS FOR THE PODCAST

ShadowBroker is a **perfect case study** for a digital-rights podcast because it embodies every tension in the surveillance debate simultaneously:

- It **aggregates surveillance** (CCTV cameras, tracking flights, ships, satellites) and makes it available to anyone with a self-hosted instance
- It **claims privacy** ("no accounts, no telemetry, no analytics") while its own codebase has been shown to **leak operator identity** to third parties
- It **built a Telegram OSINT scraper** that pulls public channel previews from war/conflict feeds and geoparses them onto a map
- It **watches 22,000+ CCTV cameras** in real time — the kind of capability that authoritarian states would envy
- It **includes a Recon Toolkit** (DNS, WHOIS, sanctions, BGP, Shodan, CVE lookups) that can be used for both legitimate security research and reconnaissance
- Its **AI agent channel** means an LLM can autonomously query all 40+ data layers, run subnet scans, and place intelligence pins on a map

This is the **democratization of surveillance** in its purest form: tools that were once exclusive to nation-state intelligence agencies are now available to anyone with a Docker instance and an API key.

---

## 3. ETHICAL TENSIONS & CIVIL LIBERTIES CONCERNS

### Tension #1: Transparency vs. Surveillance Normalization

The project's philosophy is that *"a surprising amount of global telemetry is already public"* and that aggregation serves transparency. But **aggregation changes the nature of the data**. Individual data points (an ADS-B broadcast, an AIS signal, a CCTV feed) are mundane. Combined, they create a **comprehensive surveillance posture** that can track individuals, map military movements, and identify infrastructure vulnerabilities.

**Podcast angle:** Is there a qualitative difference between "data is public" and "we've built a unified surveillance platform from public data"? When does aggregation become its own form of power?

### Tension #2: The "No Privacy Guarantee" Admission

The project's own documentation (the InfoNet testnet section) explicitly states:

> **"Do not transmit anything sensitive on any channel. Treat all channels as open and public for now."**

This is remarkable — the developers built a messaging system that is **obfuscated but NOT end-to-end encrypted**, with a public hashchain ledger for governance, and radio transmissions that are "inherently public." They then added a full warning: *"Privacy is not guaranteed yet."*

**Podcast angle:** This is a microcosm of the broader tech industry pattern: build first, ask questions later, admit limitations in the fine print. The question is whether "we told you it's not private" is an adequate defense when the default user experience assumes otherwise.

### Tension #3: The Privacy-Parsing Paradox (Issues #356, #361, #354, #350, #351, #353, #352)

Community auditor **tg12** filed a cascade of issues revealing that ShadowBroker's README claims *"No user data is collected or transmitted"* — but the codebase was:

1. **Loading Google Fonts** from the browser on first paint (Issue #353)
2. **Leaking sessions to CartoCDN** via default basemap tiles (Issue #354)
3. **Making browser-direct calls** to Nominatim, RestCountries, and Planetary Computer (Issue #351)
4. **Falling back to client-side Nominatim autocomplete** in the LocateBar (Issue #352)
5. **Reusing one stable operator handle** across all outbound requests to Nominatim, Shodan, Broadcastify, OpenMHz, GDELT, etc., enabling **cross-provider correlation** (Issue #361)
6. **Claiming zero transmission** while performing all of the above (Issue #356)

tg12's issue #356 was labeled **P1, Critical severity, Confidence: Confirmed**. It argued that a user who deploys ShadowBroker in a "sensitive environment" based on the README's zero-transmission claim would later discover that the browser contacted multiple third-party services.

**Podcast angle:** This is the **"privacy-washing"** problem. The project markets itself as privacy-respecting while its actual behavior contradicts that claim. It's the same pattern seen in major tech companies — the privacy policy is written to satisfy legal requirements, not to inform actual user behavior. The difference here is that it's an *open-source* project, meaning the code is available for audit, but almost nobody reads 50,000 lines of Python/JavaScript before deploying.

### Tension #4: The AI Agent — Autonomous Surveillance

The OpenClaw/agent command channel is arguably the most ethically fraught feature. Any compatible AI agent (Claude, GPT, LangChain, custom) can:

- Query all 40+ data layers
- Run **subnet scans** (Shodan InternetDB host discovery)
- Place **intelligence pins** on a map with confidence scores
- **Fly the operator's map view** to any coordinate
- Access **recon toolkits** (IP/DNS/WHOIS/sanctions/CVE/MAC/GitHub/leaks)
- Interact with the **InfoNet mesh** as a full peer
- Participate in **Sovereign Shell governance** (file petitions, vote, stake on resolutions)

The tier-gated access model (`restricted` = read-only, `full` = read + write + inject) means that with the right HMAC secret, an AI agent has **near-total operational control** of the surveillance platform.

**Podcast angle:** This is the **"AI as surveillance operator"** scenario. When an LLM can autonomously identify targets, run recon, and place pins on a map, who is responsible for what it does? The project says it "does not bundle an LLM" — but it provides the surface. This is the exact question the EU AI Act and other frameworks are grappling with: if you build the platform, do you bear responsibility for how others use it?

### Tension #5: Unauthorized Scraping & Third-Party Harm (Issue #188)

A commercial threat-intelligence company (Crowd Threat Limited) discovered that ShadowBroker contained a **scheduled function (`fetch_crowdthreat()`) that hit their `/threats` endpoint daily at 12:00 UTC** without any license agreement. This caused:

- **Unauthorized data scraping** of proprietary platform data
- **Service disruption** — aggregated requests from multiple developer machines created DoS-adjacent traffic

The maintainer's initial response was *"I just saw this and will be updating the source code immediately. It wasn't my intent."* Community auditor tg12 responded:

> *"You didn't accidentally create a scheduled function that hits someone else's `/threats` endpoint daily. That is not a typo. That is not 'virality.' That is a design decision sitting in the source code."*

tg12 further argued: *"They look impressive, go viral, get cloned, and then the hidden garbage inside them becomes someone else's problem."*

**Podcast angle:** This is the **"vibe-coded surveillance"** problem. AI-assisted development can produce functionally impressive projects that contain hidden behaviors — automated calls to third-party services, unlicensed data scraping, privacy contradictions — that the developer may not even be aware of. When the codebase scales to thousands of self-hosted instances, these hidden behaviors become **systemic externalities**. Who is liable? The developer? The platform? The user who deploys it?

### Tension #6: The "Knowledge Should Be Free" Ideology vs. Real-World Harm

The project's core premise is that telemetry is already public, so aggregation is harmless. But consider:

- **Tracking Air Force One** and presidential aircraft in real time could be used for assassination planning or stalking
- **22,000+ CCTV cameras** streamed live means anyone can watch any public space, at any time, from anywhere
- **Shodan integration** means anyone can search for internet-connected cameras, SCADA systems, databases — and plot them on a map
- **Telegram OSINT scraping** of war/conflict feeds could be used for propaganda targeting or censorship evasion... or for surveillance of journalists and activists
- **SAR ground-change detection** can identify monitoring of private land, deforestation, or military activity — useful for environmental activists or for authoritarian border surveillance

**Podcast angle:** The "data is public" argument is a **privacy fallacy**. Individual data points may be public, but the *convergence* of public data creates private information. Your movements are "public" when you walk down the street. But if you combine street-level CCTV, AIS vessel tracking, ADS-B flight tracking, cell tower data, and social media geotags, you can reconstruct someone's entire life. ShadowBroker does exactly this — and it's available as a Docker container.

---

## 4. THE COMMUNITY AUDITOR: TG12 AS A CASE STUDY

One of the most compelling threads in the ShadowBroker ecosystem is the role of **tg12**, a community auditor who:

1. Filed **8+ privacy and data-leak issues** (all closed as fixed), revealing systematic contradictions between the project's privacy claims and its actual behavior
2. Built an external website documenting **"AI slop intelligence dashboards"** — the pattern of impressive-looking, poorly-vetted surveillance tools that cause externalities
3. Created a companion tool (**phantomstars**) to monitor **fake-engagement and low-quality GitHub repositories** — because GitHub is "increasingly being polluted by these projects"
4. Pushed back against the maintainer's "I'll fix it immediately" deflection with: *"The correct response is: disable the function, publish exactly what it did, identify every external endpoint the app calls, document the data sources and permissions, add tests around network behaviour, and audit the repo before encouraging anyone else to run it."*

**Podcast angle:** tg12 represents a new kind of digital-rights actor — the **community auditor** who operates within open-source projects to surface ethical issues that developers may not see. This is crowdsourced surveillance oversight. But it also raises questions: Should this kind of auditing be necessary? Should open-source projects have ethical review processes before features ship?

---

## 5. THE LICENSING PARADOX

ShadowBroker is licensed under **AGPL-3.0** — the most restrictive common open-source license. AGPL requires that any modified version offered as a service must also offer its source code. This is designed to prevent cloud providers from profiting from open-source software without giving back.

But AGPL doesn't address **ethical obligations**. The license ensures code availability, not responsible use. The project's legal disclaimer states: *"This tool is intended for legitimate OSINT investigation purposes only. Users are responsible for complying with all applicable laws."* This is the standard "don't blame us" posture — but it's questionable whether a legal disclaimer can ethically absolve a tool that enables 22,000-way CCTV surveillance.

**Podcast angle:** Open-source licensing addresses *code freedom*, not *use responsibility*.Should there be an "ethical license" that conditions use on human-rights impact assessments? The SFOS (Semantic Form of the OSI) and the Hippocratic License have attempted this — but they remain marginal. ShadowBroker's AGPL license is "free" by OSI standards, but is it "free" in the sense of being freedom-preserving?

---

## 6. KEY PODCAST ANGLES & TALKING POINTS

### Angle A: "The Democratization of Surveillance"
ShadowBroker puts nation-state-level surveillance capability in a Docker container. What does it mean when tracking Air Force One, monitoring 22,000 CCTV cameras, and running Shodan recon are available to anyone? Is this "transparency" or "surveillance equality"?

### Angle B: "Privacy-Washing in Open Source"
The README says "no data is collected or transmitted." The code says otherwise. This is the open-source equivalent of greenwashing — privacy-washing. When the documentation contradicts the code, and the project is 50,000 lines, who catches it? And does "we found out and fixed it" count as adequate?

### Angle C: "The AI Agent as Surveillance Operator"
When an LLM can autonomously query all 40+ data layers, run subnet scans, and place intelligence pins, we've crossed a threshold. This isn't a tool anymore — it's an **autonomous surveillance actor**. Who's responsible when the AI does something harmful?

### Angle D: "The Community Auditor as New Digital-Rights Actor"
tg12's work shows that ethical oversight of open-source surveillance is happening *in the wild*, not in corporate review boards. But it's ad hoc, unpaid, and confrontational. Should there be institutionalized ethical review for high-impact open-source projects?

### Angle E: "When 'Data Is Public' Is a Privacy Fallacy"
The aggregation of public data creates private information. ShadowBroker proves this. What's the ethical threshold? Is tracking a ship's AIS signal ethical? Is tracking that ship alongside satellite imagery, conflict data, and the owner's other assets? Where's the line?

### Angle F: "The 'Vibe-Code' Surveillance Problem"
Issue #188 (unauthorized scraping) and the privacy regressions (#356, #361) suggest that AI-assisted development can produce functionally impressive projects with hidden externalities. When the codebase is generated or guided by AI, does the developer bear less responsibility? Is "I didn't read the code" a defense?

### Angle G: "The Telegram OSINT Scraper & Journalist Safety"
ShadowBroker scrapes public Telegram channels (war/conflict feeds) and geoparses them onto a map. This could help journalists map conflict zones. But it could also be used to identify and track activists, dissidents, or journalists who use Telegram. Who gets to decide?

---

## 7. DOCUMENTS & RESOURCES TO REVIEW

| Resource | URL | Why It Matters |
|---|---|---|
| ShadowBroker repo | https://github.com/BigBodyCobain/Shadowbroker | The project itself |
| Forked repo (with RESEARCH_NOTES.md) | https://github.com/bro26man-hash/Shadowbroker | Your research copy |
| Issue #356 — Privacy claim vs. code behavior | https://github.com/BigBodyCobain/Shadowbroker/issues/356 | The "privacy-washing" case study |
| Issue #361 — Cross-provider correlation via shared operator handle | https://github.com/BigBodyCobain/Shadowbroker/issues/361 | Structural privacy leak |
| Issue #188 — Unauthorized API scraping | https://github.com/BigBodyCobain/Shadowbroker/issues/188 | "Vibe-coded" externalities |
| tg12's "AI slop intelligence dashboards" blog | https://labs.jamessawyer.co.uk/ai-slop-intelligence-dashboards/ | Broader pattern analysis |
| tg12's "phantomstars" tool | https://github.com/tg12/phantomstars | Monitoring fake-engagement repos |
| InfoNet threat model | `docs/mesh/threat-model.md` (in repo) | What the mesh actually defends against |
| Claims reconciliation | `docs/mesh/claims-reconciliation.md` (in repo) | Every claim mapped to code reality |
| Outbound data audit | `docs/OUTBOUND_DATA.md` (in repo) | What contacts third parties |

---

## 8. WHO TO INTERVIEW (PODCAST TARGETS)

1. **The Maintainer (BigBodyCobain)** — For the "withdrawn developer" angle: someone who built a viral surveillance tool, didn't anticipate the ethical implications, and is now learning as they go. How does a solo developer navigate responsibility at scale?

2. **tg12 (Community Auditor)** — For the "digital Sherlock Holmes" angle: someone who systematically audits surveillance tools for privacy contradictions. What motivates this work? Is it enough?

3. **A Civil-Liberties Lawyer** — To discuss whether AGPL licensing + "intended for legitimate use only" disclaimers provide any legal protection when a tool enables mass surveillance.

4. **A Journalist Using OSINT** — To get the flip side: someone who uses tools like ShadowBroker for legitimate investigation. How does the tool help? Where does it cross the line?

5. **A Surveillance-Countermeasure Advocate** — To discuss the adversarial angle: if ShadowBroker can track 22,000 CCTV cameras, what counter-measures are being developed? Is there an "anti-surveillance" arms race?

6. **An AI Ethicist** — For the AI agent question: when an LLM can autonomously run recon and place surveillance pins, what are the governance frameworks? Who's responsible?

---

## 9. OPEN QUESTIONS FOR FURTHER RESEARCH

- How many self-hosted instances of ShadowBroker are actually running? (No telemetry — the project claims this, but there's no way to verify.)
- Has anyone been prosecuted or harmed using ShadowBroker data?
- What is the InfoNet testnet's actual privacy posture? The threat model document claims specific protections — how does that hold up to real analysis?
- Are there any nation-state actors using this tool? (Given the capabilities, it seems inevitable.)
- What happens when the maintainer stops maintaining it? (Solo-maintainer risk — the project's SECURITY.md acknowledges "best-effort" security triage with "no SLA.")
- Could the AI agent channel be used to automate targeting? What safeguards exist?

---

*Research compiled from GitHub issue analysis, repository inspection, and community discussion review. Forked from BigBodyCobain/Shadowbroker to bro26man-hash/Shadowbroker for archival and reference.*
