# Media and Attachment Links

## Required Attachment Layout

This vault's Custom Attachment Location plugin is configured as follows:

| Setting | Value |
|---|---|
| New attachment location | `./assets/${noteFileName}` |
| Markdown URL format | `assets/${noteFileName}/${generatedAttachmentFileName}` |
| Generated attachment file name | `file-${date:{momentJsFormat:'YYYYMMDDHHmmssSSS'}}` |

These values mean an attachment for `research-notes.md` belongs in `./assets/research-notes/`, beside the note, and its Markdown URL must use `assets/research-notes/<actual-generated-file-name>`. The generated filename includes the file extension.

When creating an attachment outside Obsidian, keep the same `file-YYYYMMDDHHmmssSSS.<extension>` name and use the actual filename in its link. This layout is required for the vault's attachment workflow.

## Local Images

Store an image for a note named `research-notes.md` in `assets/research-notes/`, then use standard Markdown image syntax:

```markdown
![](assets/research-notes/file-20260913012345678.png)
```

Do not use HTML image tags. Keep the asset directory beside its note.

## Notes, Attachments, and Bases

Use standard Markdown links for notes, PDFs, audio, Bases, and other attachments:

```markdown
[Read the project plan](../Projects/project-plan.md)
[Open the source PDF](assets/research-notes/source.pdf)
[Listen to the interview](assets/research-notes/interview.mp3)
[Open the task base](../Tasks/task-tracker.base)
```

Notes and Bases are linked rather than transcluded inline.

## Embed Search Results

````markdown
```query
tag:#project status:done
```
````
