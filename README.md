# How to Publish TikTok Videos With Codex and Groniz

You can publish TikTok videos with Codex by giving it an approved video package and a connected Groniz integration. Codex runs the delivery workflow. Groniz handles OAuth, channel formatting, and delivery. The account owner still decides whether the video is original, accurate, properly licensed, correctly disclosed, and ready to publish.

Finish the video first, then freeze its approved caption and settings. Connect TikTok and choose a supported Codex access path. Codex can inspect the live channel requirements, upload the approved media, schedule one delivery, and verify the returned state. The account owner retains the final publishing decision.

This is the delivery stage of a larger business system. The [TikTok automation hub](https://groniz.com/blog/make-money-automating-tiktok) covers the models and controls that need to exist before publishing becomes worth automating.

## Start with an approved package

An instruction such as "make and post a TikTok about our product" is too broad. It combines research, creative judgment, rights review, disclosure, account choice, and an external write without identifying who approves any of them.

Prepare a package like this first:

```text
tiktok-package/
  video.mp4
  caption.txt
  settings.json
  manifest.json
  approvals/
  sources/
  rights-ledger.csv
```

The manifest should identify the content version and target account, along with the intended time zone, planned schedule, disclosure decision, approver, and exact video and caption files. If your workflow uses checksums, include them so Codex can confirm that the approved file has not changed.

The larger [TikTok content automation workflow](https://groniz.com/blog/tiktok-content-automation-workflow) explains how to move from a source packet to this approval boundary.

## Choose one Codex connection path

Groniz supports Skill, CLI, and MCP paths for Codex. Each reaches the same connector core, so choose the one that fits the environment.

### Skill

Install the Groniz CLI skill with the documented command:

```bash
npx skills add groniz/groniz-cli
```

The skill teaches Codex the delivery commands. If the environment needs non-interactive authentication, get an API key from the [Groniz API keys page](https://groniz.com/console/connectors/api-keys).

### CLI

Install the native CLI and use its browser device login:

```bash
curl -fsSL https://groniz.com/install.sh | sh
groniz auth:login
```

Use this path when Codex can run shell commands in the same workspace as the approved package.

### MCP

For Codex MCP, set the token environment variable before Codex starts, then configure the remote server to read that variable:

```bash
export GRONIZ_API_KEY="your-api-key"
codex mcp add groniz --url https://mcp.groniz.com/mcp \
  --bearer-token-env-var GRONIZ_API_KEY
```

Do not place the token in a checked-in project file. The command above is a setup example. Keep secrets in the environment and follow your organization's credential controls.

## Connect the TikTok account deliberately

Connect the intended TikTok account through [Groniz Connectors](https://groniz.com/console/connectors). Record a human-readable account name and the integration ID in the approval manifest. Before any upload, the operator should be able to distinguish a personal account from a brand or test account.

Groniz supports TikTok as one of 32+ networks, but provider capabilities differ. A settings object from another network may not apply, and an old TikTok schema may no longer be current. Ask Codex to inspect the connected integration and report its live required fields before creating a post.

If the selected Groniz path does not expose a required choice or reliable preflight view, leave the delivery uncreated. Complete the decision in the available Groniz or TikTok interface. Provider formatting support does not justify an inferred account setting.

TikTok's own [Content Posting API guidelines](https://developers.tiktok.com/doc/content-sharing-guidelines/) require creator awareness and control, current creator information, editable post choices, and post-status handling for API clients. Your connector abstracts part of that work, but your operating process should preserve the same clear account choice, preview, consent, and verification.

## Give Codex a bounded delivery instruction

Use a prompt packet that names the source of every decision:

```text
Schedule one approved TikTok delivery through the connected Groniz integration.

Approved inputs
- Video: ./tiktok-package/video.mp4
- Caption: ./tiktok-package/caption.txt
- Manifest: ./tiktok-package/manifest.json
- Target integration: <integration id and account name>
- Requested time: <ISO-8601 timestamp with offset>

Required workflow
1. Confirm Groniz authentication and the target integration.
2. Read the live TikTok integration requirements and report any missing field.
3. Verify that the manifest names the same video, caption, account, and time.
4. Upload only the approved video through Groniz.
5. Show the final mapped settings before the external write.
6. Create exactly one scheduled post after explicit approval.
7. Return the Groniz post ID, scheduled time, and current state.
8. If the result is uncertain, reconcile it before any retry.

Do not edit the video or caption. Do not substitute another account or time.
Do not publish immediately. Do not create a second post to test the connection.
```

The prompt does not hardcode TikTok fields. Current requirements come from the connected integration; editorial decisions come from the manifest.

Treat it as a prompt template rather than a universal command transcript. The Groniz skill, CLI, or MCP tools available to Codex determine the actual tool names and returned fields. Completion means obtaining a reconciled delivery record for the approved package. It does not depend on a particular sequence of commands.

## Inspect the mapping before the write

Codex should present a compact preflight record:

| Check | Expected evidence |
| --- | --- |
| Authentication | Groniz account or organization confirmed |
| Integration | TikTok ID and account name match the manifest |
| Media | Approved local file identified and upload completed |
| Caption | Exact approved text loaded without rewriting |
| Required settings | Every live required field has a reviewed value |
| Disclosure | Matches the final editorial and commercial review |
| Schedule | ISO timestamp and human-readable local time agree |
| Duplicate check | No existing delivery for the same content version and time |

If a required choice is absent, stop and ask the owner. An agent should not invent privacy choices, interaction settings, commercial disclosure, or account-level limits.

## Keep media upload and post creation separate

Uploading the approved file creates a platform-ready media reference, but it does not grant permission to publish. Record the uploaded media reference in the run log. Use it only after the settings and schedule pass review.

A similar filename is not enough. Match the uploaded object to the manifest instead of passing an arbitrary external URL or unapproved local file.

If the upload fails, keep the post uncreated. If post creation returns an uncertain result, look up the scheduled posts or returned identifier before retrying. A second blind attempt can produce duplicate public content.

## Verify more than command success

An accepted schedule should return a Groniz post ID, time, target integration, and current state. Save those fields with the content version:

```json
{
  "content_version": "tt-2026-08-20-01",
  "target": "brand-tiktok",
  "groniz_post_id": "returned-id",
  "scheduled_at": "2026-08-20T10:00:00+08:00",
  "state": "queued",
  "verified_at": "timestamp"
}
```

At the scheduled time, confirm the later state and public result available to you. Processing can take time, and a queued record is not proof that a post is public. Preserve provider error text and original state labels when a delivery fails.

The [agent-to-channel publishing checklist](https://groniz.com/blog/agent-to-channel-publishing-checklist) covers the same source, review, delivery, and verification pattern for other networks.

## Know what the workflow does not do

This setup cannot make the video eligible for monetization, create original footage, clear music rights, decide whether a claim is fair, or guarantee reach. Codex operates the workflow you define, and Groniz performs the connected delivery step.

Review TikTok's current [AI-generated content guidance](https://support.tiktok.com/en/using-tiktok/creating-videos/ai-generated-content) when realistic synthetic media appears, and its [commercial-content guidance](https://support.tiktok.com/en/business-and-creator/creator-and-business-accounts/promoting-a-brand-product-or-service) when the post promotes a brand, product, or service.

After the package passes those gates, confirm TikTok on the [supported channels page](https://groniz.com/channels) and run the bounded delivery through [Groniz Connectors](https://groniz.com/console/connectors). This gives the operator a short, inspectable path from an approved asset to a verified delivery record.
