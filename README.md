# E Sky Trading Website Archive

English | [中文](README.zh-CN.md)

A sample website I built for **E Sky Trading (Technology) Co., Ltd.** (云联天下), a real company, now preserved as a bilingual static site.

**Live demo:** https://otyswkydo.github.io/yunlian-co-demo/ ([English version](https://otyswkydo.github.io/yunlian-co-demo/en/))

- Online as a self-hosted WordPress site from **Sep 2025 to Aug 2026**
- The site was hacked while it was live. When recovering it, I had Claude throw away every program file from the server, keep only the content and images, and rebuild it from official sources
- Now a free static archive on GitHub Pages: 5 pages each in Chinese and English, 327 internal links checked, 0 broken

## About the project

E Sky Trading is a company based in Canada. Its website describes three lines of business: study abroad, immigration and visa services; company registration, operations and government grant applications; and import and export trade support. The site has 5 pages: Home, About, Services (with an FAQ), Contact, and a case study page, Growing Together, about how the company helped an Eastern skincare brand open its first store in the Toronto area. The whole site uses 60 images.

Like the [METIS Academy website](https://github.com/otyswkydo/yunlian-metis-demo), this was a practice site for me. It's built with WordPress and the [Astra](https://wpastra.com/) theme. I started the pages from an Astra template (Starter Templates) and then changed them into E Sky Trading's content. The blocks on the pages come from the [Spectra](https://wpspectra.com/) plugin, and the contact form uses [SureForms](https://sureforms.com/).

It ran on the same server as my other two sites. How I ran and recovered them is described in [the Canada-China Intangible Cultural Heritage Association website's README](https://github.com/otyswkydo/yunlian-legacy-demo#readme). Of the three sites, this was the last one exported, so for me it was also a test: would the export process fixed up on the first two sites work on a new site without changes?

## Turning it into a static site

Mostly, yes. The changes I had Claude make for METIS, like adding the missing `/` to links and not fetching cart pages on a site without a shop, simply worked here. Claude's very first export had 0 fetch errors, 0 duplicate pages and 0 broken links. Only three things needed new work:

- Security checks before and after the export (see problem 1 below)
- The English translation, which I had Claude do. The lookup file has 54 entries: 52 translations (about 2,160 Chinese characters into about 1,400 English words), plus 2 entries that remove duplicates from the English version, because the company's full name and slogan on the home page already have English right next to them.
- Capital letters in the English headings (see problem 2 below)

The result:

- 5 pages in each language plus a "page not found" page: 11 web pages, 217 files, 13 MB.
- Every page has an EN / 中文 switch in the header.
- The contact form looks the same as before. Pressing Submit shows a message saying forms can't be submitted on the static demo. Claude tested it in the browser, and submitting doesn't send any request.
- The link checker Claude wrote opened 70 URLs and 327 link references, with 0 broken.

## Problems I ran into

### 1. The site had been hacked

**The problem:** After the server expired, I needed to recover the three sites from a snapshot. Before the recovery, I had Claude run a security check on the snapshot, and it showed that a backdoor had been planted the day after the site was set up. WordPress was installed on September 1, 2025. On September 2 a backdoor plugin disguised as an SEO tool appeared, and on September 3 three administrator accounts I didn't recognize were created. A few hours later, a Google site-verification file showed up in the site's root folder, which means the attacker most likely added the domain to their own Google Search Console to use it for spam. Later, a block of encrypted code was added to the site's entry file `index.php`, `robots.txt` was changed to point at 20 spam sitemaps, and more than 70 backdoor files and hidden folders were spread across the site. File dates can be faked by an attacker, but new files kept appearing from September 2025 to June 2026. In other words, they had access to the site for about 10 months, and I never noticed while it was running.

**What I found:** Claude's check found that the site's actual content, its pages and text, was clean. No spam links or scripts had been added. What had been changed were the program files, the entry file `index.php`, `robots.txt` and the admin accounts. There's no way to know for sure how they got in. Common causes are a weak admin password or a plugin with a security hole. A plugin for managing server files from inside the browser had been installed at some point. Plugins like that have had serious security holes before, so it may have been the way in, or it may have been a tool the attackers installed themselves. The other two sites on the same server showed no signs of a break-in, so the problem was most likely with this site itself.

**After the fix:** I had Claude handle this site specially in the recovery script:

- No program files from the snapshot are used at all. WordPress comes from the official Docker image, and the theme and plugins are downloaded again from wordpress.org at their original versions.
- Only the uploaded images are copied from the snapshot. Everything else is thrown away.
- The 3 unknown admin accounts are deleted, the admin password is reset, and the file manager plugin and its leftover data are removed.
- Before exporting, a scan script looks for backdoor patterns and compares every program file with the official version from wordpress.org.

Before this export, I had Claude run the scan again: the WordPress core files matched the official ones, all 6 plugins matched their official versions, nothing suspicious was found, and the site has only 1 user left. After the export, I had Claude pull 17 signs of the break-in from the incident notes (the unknown accounts and their emails, the backdoor plugin's name, the spam sitemap names, common ways of hiding malicious code, and so on) and search every exported file for each one. All 17 came back 0. The exported site has no `robots.txt`, no sitemaps and no `.php` files, and no page links to any outside website.

Before, this site ran for about 10 months with backdoors and strangers as administrators. Now it's a plain static website: no admin panel, no database, and no program running on a server, so there's almost nothing left for an attacker to target.

**What I learned:** A site that opens normally isn't necessarily safe. From where I sat, this site always looked fine, but its entry file had been changed and someone else had control of it the whole time. Search engines were probably seeing something quite different. From now on I'll only install plugins I really need, use strong admin passwords with two-factor authentication, and keep plugins and themes updated. Most importantly, I'll back up the site's files and database somewhere other than the server. Keeping a full snapshot is the only reason none of the content was lost this time.

### 2. All the English headings were lowercase

**The problem:** Once the English version was done, Claude checked the pages in the browser and noticed every heading was in lowercase, like "our work: one-stop support from e sky trading…". Even the company name E Sky Trading showed up as "e sky trading".

**What I found:** Claude looked at the theme settings and found that this site's headings were set to "all lowercase" in Astra. That was part of the original design. Chinese has no upper or lower case, so it never showed on the Chinese version, but in English it affected every heading.

**After the fix:** Claude offered two options: leave it as it was, or turn off the lowercase only in the English version. I chose the second, and had Claude add a rule to the English export: only when the theme's heading setting is "all lowercase", the English version turns that off and shows headings with their original capital letters. The Chinese version keeps the original design, and the other two sites aren't affected. Before, the company and brand names in English headings were all lowercase. After, "E Sky Trading" and "YOUBO LADY" display correctly.

Once the headings showed their original capitals, one spelling that had been hidden appeared: the case study's main heading was actually written "growing Together". I had Claude back up the database first, change it to "Growing Together", and then compare the page character by character to confirm nothing else changed.

**What I learned:** A design that works in one language can cause problems in another. When making a multilingual version, it's not enough to translate the words. You also have to look at how they actually appear on the page.

## Tech overview

- Website: WordPress, Astra theme, Spectra blocks, SureForms
- Server (while it was live): Ubuntu, 1Panel, Docker, Cloudflare
- Recovery and security: Docker on OrbStack, WP-CLI, a backdoor pattern scan, checksum comparison against wordpress.org
- Export: Bash, wget, Perl, PHP
- English version: a Chinese–English lookup file; the export builds the English pages under `/en/`
- Testing: a link checker (Python), plus testing in the browser on desktop and mobile
- Publishing: Git, GitHub CLI, GitHub Pages

## What I did and what Claude did

- What I did: set up the server; built the website starting from an Astra template, replaced the content, adjusted the layout and put it online; kept a full snapshot when the server expired; ran the recovery script Claude wrote on my own Mac.
- What I had Claude do: Claude wrote the recovery script, the security scan, the export scripts, the link checker and the rest of the code. For this archive, Claude also ran the security scan, the export, the link and privacy checks, the English translation and the browser tests.
- What I decided and approved: the repository name, keeping the contact email, making an English version, how to handle the lowercase English headings, changing "growing Together" to capitals, and how each folder is described on GitHub. At every step I looked at Claude's results and confirmed them before moving on, and I ran the final publish command myself.

## Credits

The website describes E Sky Trading (Technology) Co., Ltd., a real company, and its content and branding belong to them. The page layouts come from an Astra template. The brand name and description in the case study belong to that brand. This repository is a non-commercial archive and demo, and the contact form doesn't send anything.

Projects used: [WordPress](https://wordpress.org/), [Astra](https://wpastra.com/), [Spectra](https://wpspectra.com/), [SureForms](https://sureforms.com/), [1Panel](https://github.com/1Panel-dev/1Panel), [OrbStack](https://orbstack.dev/), [WP-CLI](https://wp-cli.org/), [GNU Wget](https://www.gnu.org/software/wget/), [GitHub Pages](https://pages.github.com/).
