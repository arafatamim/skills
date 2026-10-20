# skills

A collection of agent skills in the standard **Agent Skills** format.

## Format

Each skill is a self-contained directory:

```
<skill-name>/
├── SKILL.md          # YAML frontmatter (name, description) + instructions
└── references/       # optional supporting docs, loaded on demand
```

`SKILL.md` frontmatter fields:

- `name` — must match the directory name (lowercase, hyphenated)
- `description` — starts with the trigger condition ("Use when …"), so the agent knows when to load it
- `version` — semantic version of the skill

## Skills

| Skill | Description |
|---|---|
| [`bd-shopping`](bd-shopping/SKILL.md) | Research any purchase for a buyer in Bangladesh — pins requirements, shortlists from independent evidence, verifies BDT prices/stock at local retailers, checks warranty & authenticity, computes landed cost for imports, returns a buy/wait/skip verdict. Category playbooks for electronics, appliances, clothing, beauty, groceries, furniture, books, and medicines. |

## Usage

Copy a skill directory into your agent's skills folder (for Hermes: `~/.hermes/skills/`, or a profile's `skills/` directory), or point your tooling at this repo as a skill source.
