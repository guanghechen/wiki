# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a personal knowledge base repository for public notes covering:
- Programming language learning materials (e.g., Rust, and other languages)
- Application usage notes and FAQs
- Technical documentation and reference materials

The user expects detailed, professional, and thorough explanations when asking technical questions. Be patient and comprehensive in responses.

## Repository Structure

The repository is organized to store markdown-based notes and documentation. Files in `local/` are gitignored and contain private notes that should never be accessed.

## Working with This Repository

- **Note Creation**: Notes should be well-structured markdown files with clear headings and code examples where applicable
- **Technical Depth**: When answering questions, provide in-depth technical explanations rather than surface-level summaries
- **Code Examples**: Include practical, runnable code examples when discussing programming concepts
- **Language-Specific Content**: When creating notes for programming languages, include:
  - Syntax examples with explanations
  - Common patterns and idioms
  - Pitfalls and best practices
  - Links to official documentation where relevant

## Knowledge Management

When adding or updating content in this repository, follow these principles to maintain an efficient, interconnected knowledge base:

### Before Adding New Content

1. **Check for Existing Content**: Always search the repository for overlapping or related knowledge before creating new notes
   - Use grep/search to find similar topics, concepts, or code examples
   - Review related files to understand existing structure and coverage
   - Identify potential areas of overlap or redundancy

2. **Avoid Redundancy**: If similar content exists:
   - **Consolidate**: Merge related content into a single, comprehensive note
   - **Reference**: Link to existing content instead of duplicating it
   - **Extend**: Add to existing notes rather than creating parallel documents
   - **Refactor**: Reorganize if the existing structure doesn't accommodate new information well

3. **Create Interconnections**: Build a web of knowledge
   - Add cross-references between related topics using markdown links
   - Create index files or table-of-contents when appropriate
   - Use consistent terminology across documents to improve searchability
   - Consider creating a "See also" section in notes to link related content

### Content Organization Strategies

- **Hierarchical Structure**: Group related topics in directories (e.g., `coding/rust/`, `os/win/`)
- **Atomic Notes**: Each file should cover a specific, well-defined topic
- **DRY Principle**: Don't Repeat Yourself - write once, reference many times
- **Progressive Disclosure**: Start with overview documents that link to detailed sub-topics
- **Consistent Naming**: Use clear, descriptive filenames that indicate content scope

### When Updating Knowledge

Before adding new information:
1. Search for existing coverage of the topic
2. Assess whether to merge, extend, or create new content
3. Update cross-references in related documents
4. Ensure consistency with existing conventions and style
5. Consider impact on overall knowledge structure

The goal is a well-organized, navigable knowledge base where information is easy to find, understand, and maintain.

## Git Workflow

- Never commit changes unless explicitly instructed
- Changes should remain in the working directory for manual review
