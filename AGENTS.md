# Obsidian 笔记库

- 本仓库是 Obsidian 笔记库；笔记内容须使用标准 Markdown 语法。
- 不要使用 Obsidian Wiki 链接或嵌入，包括 `[[笔记]]` 和 `![[图片.png]]`；内部笔记、附件和外部资源一律使用标准 Markdown 链接。
- 不要使用 HTML `<img>` 标签。
- 图片放在笔记所在目录的 `assets/<笔记文件名>/` 中；链接格式必须为：

  ```md
  ![](assets/<笔记文件名>/<图片文件名>)
  ```

  `<笔记文件名>` 不含 `.md` 后缀。
- 新增的图片、PDF、音频等附件必须遵循 `Custom Attachment Location` 插件配置：附件目录为 `./assets/${noteFileName}`，Markdown URL 格式为 `assets/${noteFileName}/${generatedAttachmentFileName}`，自动文件名格式为 `file-${date:{momentJsFormat:'YYYYMMDDHHmmssSSS'}}`。手动新增附件时也必须保持相同的目录与 `file-YYYYMMDDHHmmssSSS.<extension>` 命名规则，并使用实际文件名（含扩展名）建立相对 Markdown 链接。
