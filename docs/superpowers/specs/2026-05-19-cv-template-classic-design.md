# CV Template Redesign — Classic Serif Format

**Date:** 2026-05-19  
**Status:** Approved

## Goal

Replace `templates/cv-template.html` entirely with a clean, classic serif layout matching the Kiran Kumar resume reference. Same filename, same placeholder tokens — no downstream pipeline changes required.

## Typography & Page

- Body font: `Georgia, serif` (system font, no embedding required)
- Body size: 11px, line-height 1.6
- Page: white background, single column, standard A4 margins
- Print color-adjust preserved for PDF generation via Playwright

## Header

- Name: centered, bold, ~26px
- Contact line: centered, single row
- Format: `phone · email · LinkedIn · website · location`
- Separator: `·` (middle dot)
- Links: phone, email (`mailto:`), LinkedIn, website (`{{PORTFOLIO_URL}}` / `{{PORTFOLIO_DISPLAY}}`) — all in muted blue matching section header color
- No gradient bar (removed)

## Section Headers

- Style: ALL CAPS, bold, blue (`#2563eb`), ~11px
- Rule: full-width 1px gray (`#e2e2e2`) horizontal line underneath
- No background, no tags, no gradient

## Sections (in order)

1. **Profile Summary** — `{{SECTION_SUMMARY}}` / `{{SUMMARY_TEXT}}`
2. **Work Experience** — `{{SECTION_EXPERIENCE}}` / `{{EXPERIENCE}}`
3. **Technical Skills** — `{{SECTION_SKILLS}}` / `{{SKILLS}}`
4. **Education** — `{{SECTION_EDUCATION}}` / `{{EDUCATION}}`

Removed sections: Core Competencies, Projects, Certifications.

## Work Experience Entries

- Job title: bold, left-aligned (rendered inside `{{EXPERIENCE}}` block)
- Company + Date: same line, flex space-between — company italic left, date italic right
- Bullet points indented, inline `<strong>` for bold keywords

## Technical Skills

- Bulleted list
- Each line: `<strong>Category:</strong> values`
- Matches reference format exactly

## Education

- University name: bold left + date right-aligned (same line, flex space-between)
- Degree: plain text on next line
- GPA/grade: plain text on next line below that

## Placeholder Contract

All existing tokens preserved — no changes to `generate-pdf.mjs` or modes:

| Token | Usage |
|---|---|
| `{{LANG}}` | `<html lang="">` |
| `{{PAGE_WIDTH}}` | `.page` max-width |
| `{{NAME}}` | Header name |
| `{{PHONE}}` | Contact line |
| `{{EMAIL}}` | Contact line (mailto link) |
| `{{LINKEDIN_URL}}` / `{{LINKEDIN_DISPLAY}}` | Contact line |
| `{{PORTFOLIO_URL}}` / `{{PORTFOLIO_DISPLAY}}` | Website — renders as `manukashyap.in` |
| `{{LOCATION}}` | Contact line |
| `{{SECTION_SUMMARY}}` / `{{SUMMARY_TEXT}}` | Profile Summary section |
| `{{SECTION_EXPERIENCE}}` / `{{EXPERIENCE}}` | Work Experience section |
| `{{SECTION_SKILLS}}` / `{{SKILLS}}` | Technical Skills section |
| `{{SECTION_EDUCATION}}` / `{{EDUCATION}}` | Education section |

Removed tokens (no longer in template): `{{COMPETENCIES}}`, `{{SECTION_COMPETENCIES}}`, `{{PROJECTS}}`, `{{SECTION_PROJECTS}}`, `{{CERTIFICATIONS}}`, `{{SECTION_CERTIFICATIONS}}`.

## Follow-on (out of scope for this task)

The `pdf` mode may still try to generate content for Projects/Certifications/Competencies. A follow-on task should clean up the mode prompts to skip those sections.
