# cinematic-scroll-site

> Made by [MaPix webstudio](https://ma-p.eu) – Digitales Handwerk & Innovation. Websites, Wartung und IT-Dienstleistungen aus Tirol.

A Claude Code skill for building cinematic, scroll-driven landing pages: AI-generated hero video is turned into frames and scrubbed on scroll (canvas + GSAP ScrollTrigger + Lenis), with large typographic scenes on top.

## Install

Copy the skill folder into your Claude Code skills directory:

```bash
git clone https://github.com/mannarch/cinematic-scroll-site.git
mkdir -p ~/.claude/skills
cp -r cinematic-scroll-site/skills/cinematic-scroll-site ~/.claude/skills/
```

Then ask Claude Code, for example:

> Build a cinematic scroll landing page for [product]. Dark design, 6 scenes, hero video scrubbed on scroll.

## What is inside

- `skills/cinematic-scroll-site/SKILL.md` the skill

## Works best with

An image generator (e.g. Nano Banana MCP), an image-to-video tool (e.g. Higgsfield MCP), `ffmpeg`, and a design skill such as `frontend-design`.

## Notes

Generating good assets takes iterations. Keep total frame weight under ~10 MB and provide a poster/fallback for reduced motion.

## About

Maintained by Manfred Pinggera, [MaPix webstudio](https://ma-p.eu) · office@ma-p.eu. Need a cinematic website for your business? Get in touch.

## License

MIT
