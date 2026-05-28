# CORNERSTONE Open Platform — Terms of Use

**Effective date:** 24 May 2026
**Version:** 1.1.0
**Service:** CORNERSTONE Open Platform — OPENSOURCE PDK live monitor ("the **Service**" or "the **Dashboard**")
**Operator:** CORNERSTONE Open Platform ("**we**", "**us**", "**our**")

> **Friendly summary.** The Dashboard is a free, single-file web app for
> tracking photonics PDK content. It runs entirely in your browser — we do
> not run a server, we do not collect your data, and your dashboard state
> stays on your own device. Hardware content shared through the Service is
> released under the **TAPR Open Hardware License v1.0**, so you can make,
> use, and share it. The Service is offered **as is** with no warranty.
> By using the Dashboard you agree to these terms.

---

## 1. Using the Service

When you open the Dashboard you ("the **User**") agree to use it in line
with these terms. If anything here doesn't work for you, just close the
tab — there's nothing to uninstall.

## 2. What the Service is

A self-contained HTML application that lets you curate, visualise, and
synchronise Process Design Kit (PDK) building-block data for
silicon-photonics research and prototyping. It is provided **free of
charge**. There is no central account.

If you choose to connect the Dashboard to a third party (for example,
GitHub) using your own credentials, that connection is governed by that
third party's own terms (see §10).

## 3. Who can use it

Please use the Service only if you are old enough to do so under the
rules of where you live (in most places that's 13+). If you're using
the Dashboard for an organisation, please make sure you have permission
to accept these terms on its behalf.

## 4. Roles and passwords

The Service has three local roles — **Admin**, **User-Admin**, and
**Read-only** — protected by passwords you set yourself and that stay
in your browser. We never see, store, or transmit those passwords.

A few practical things:

- Keep your passwords to yourself.
- Anything done in your dashboard is on you.
- **Take backups.** Use **Backup (.json)** or **Backup (.zip)** — we
  can't recover anything for you because we don't have a copy.

## 5. Be a good citizen

Please don't use the Service to:

a. break the law;
b. step on anyone else's intellectual-property, privacy, or contractual rights;
c. upload or sync malware, illegal content, or content that's defamatory, harassing, or hateful;
d. try to get unauthorised access to other systems via the GitHub integration, or attack them;
e. repackage and redistribute the Dashboard in a way that hides its origin or strips these terms;
f. remove or hide the copyright, licence, or attribution notices.

We may, but aren't required to, take down public mirrors or links if
they're reported to break these rules or applicable law.

## 6. Your content

"**User Content**" means anything you create or upload through the
Service — building-block metadata, S-parameter files, layouts, test
data, photographs, notes, uploaded files.

- You keep **all rights** in your User Content.
- You give us **no licence** to it, because none of it reaches us in
  the first place. It stays on your device or in repositories you
  control (for example, your own GitHub repository).
- When you choose to publish or share it, you're responsible for
  making sure you have the right to do so and for picking an
  appropriate licence for that content.

## 7. The Dashboard itself

The Dashboard source code and accompanying user manual are © 2026
CORNERSTONE Open Platform and are made available free of charge under
these terms, alongside any additional open-source notices that ship
with a given release. The CORNERSTONE name and logos remain ours;
fair-use attribution is welcome, broader use is not implied.

## 8. Open hardware (TAPR OHL v1.0)

Hardware design content distributed *through* the Service — including
building-block layouts (.gds / .svg), schematics, and reference design
files marked as "open hardware" — is licensed under the **TAPR Open
Hardware License Version 1.0** (the "**TAPR OHL**"). The full text
ships with every release as `LICENSE-TAPR.txt` and is also viewable
from the footer **Licenses** link inside the Dashboard.

In plain English (the licence text controls if there's any conflict):

- You may **make, use, sell, offer for sale, and distribute** products
  based on the licensed designs.
- **Pay it forward** — derivative designs you distribute must carry
  the same licence, and you must make the source design files
  available.
- **Keep the credit** — preserve attribution and the licence notice.
- The hardware is provided **without warranty**.

If a specific design file ships under a different open-hardware or
open-source licence (for example, CERN-OHL or a Creative Commons
licence), that file's own header wins for that file.

## 9. Privacy & data

We've designed the Service around **minimal data**.

**What we collect about you:** nothing. The Dashboard does not send
analytics, telemetry, or usage data back to us. There is no
server-side account, no central database, no behavioural tracking.

**What the Service handles on your device:** building-block metadata,
statuses, categories, notes; passwords you set for Admin / User-Admin
/ Read-only mode (hashed before storage); files you upload (layouts,
S-parameters, characterisation data, images, documents); and,
optionally, a GitHub Personal Access Token if you turn on GitHub sync.

This data sits in your browser's `localStorage` and `IndexedDB`. It
**doesn't reach us**. You can wipe it at any time by clearing your
browser storage for the page's origin, or by using the in-app
**Reset** button.

Common data-protection rights — access, correction, deletion,
restriction, portability, objection — apply to data held about you.
Because we hold none, you exercise those rights directly against your
own browser storage and against any third party (for example, GitHub)
that you have chosen to link.

The Service is not aimed at children. We don't knowingly collect data
from children because we don't collect data from anyone.

## 10. Third parties

When you supply a GitHub Personal Access Token, the Dashboard uses it
client-side to call GitHub's API on your behalf. Your use of GitHub is
then governed by
[GitHub's Terms of Service](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service)
and the
[GitHub Privacy Statement](https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement).
We have no control over, and aren't responsible for, the content,
policies, or practices of any third party.

## 11. No warranty

The Service is provided "as is" and "as available", with no warranty
of any kind — express or implied. That includes no implied warranties
of merchantability, fitness for a particular purpose, accuracy, or
non-infringement. We don't promise the Service will be uninterrupted,
error-free, or free of harmful components.

The Dashboard is a **research and prototyping tool**. Please
independently verify any output before relying on it for fabrication,
publication, or commercial use.

## 12. Termination

These terms apply for as long as you use the Service. You can stop at
any time by deleting your local copy of the Dashboard and clearing
your browser storage. We may at any time pause or take down any public
mirror or hosted instance of the Service. Sections 6 (Your content), 7
and 8 (Licensing), 9 (Privacy), and 11 (No warranty) carry on after
you stop using the Service.

## 13. Changes to these terms

We may revise these terms from time to time. The Dashboard remembers
which version you accepted; when we publish a new version you'll be
asked to read and agree to it on your next launch. Continuing to use
the Dashboard after that counts as acceptance.

## 14. Friendly resolution

If anything in these terms — or anything about the Service — doesn't
sit right, please get in touch with us first (see §15). We'll do our
best to sort it out by talking. If something does have to be decided
formally, the rules of where you live apply, and we both agree to
behave reasonably.

## 15. Contact

For privacy questions, licence questions, or any notice about these
terms, please reach the Operator at the email address shown in the
**About** dialog of your dashboard, or open an issue on the project's
public GitHub repository.

---

*End of CORNERSTONE Open Platform Terms of Use — Version 1.1.0
(24 May 2026).*
