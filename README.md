# Youseful Skills

> [!IMPORTANT]
> Please remember:
>
> - Only install the few skills the AI actually needs for the current task.
> - Installing hundreds of skills wastes tokens, increases context usage, and slows down initial session startup.

## What is in this repo

- `skills.sh` — one-command installer for 300+ curated third-party skills.
- `skills/` — original skills authored here.

## Original skills

### current-sources

Research before answering instead of relying on training data. Routes each
question type to the right source, pins the library version, triages
deprecations, and flags anything unverified.

```bash
npx skills add yousefsaad47/youseful-skills -s current-sources -g
```

### propose-before-edit

Show the exact proposed change and wait for explicit approval before writing,
editing, or running anything that alters state. Proposals stay high-level to
save tokens — full detail only after approval.

```bash
npx skills add yousefsaad47/youseful-skills -s propose-before-edit -g
```

<div align="center">
  <img src="https://github.com/user-attachments/assets/08b038f5-071a-4539-9e96-d835e3cf1704" width="32%" alt="Skill Screenshot 1" />
  <img src="https://github.com/user-attachments/assets/82198436-cb6b-406b-9d73-78e9602bb17b" width="32%" alt="Skill Screenshot 2" />
  <img src="https://github.com/user-attachments/assets/5807c40a-410b-43ad-951c-8dec9d359685" width="32%" alt="Skill Screenshot 3" />
</div>
