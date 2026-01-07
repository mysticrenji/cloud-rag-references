# Claude Instructions for Cloud RAG References Project

## Project Overview
This is a documentation project comparing cloud RAG (Retrieval-Augmented Generation) architectures across Azure, AWS, and Google Cloud Platform. The content is published as GitHub Pages.

## Project Structure
- **README.md**: Main documentation with architecture diagrams, comparisons, and pricing details
- **LICENSE**: Project license file
- **.github/workflows/**: CI/CD pipeline for automated publishing

## Instructions for Claude

### 1. Documentation Standards
- Use clear, concise technical writing
- Maintain consistency in terminology across all cloud providers
- Include Mermaid diagrams for architecture visualizations
- Use tables for feature and pricing comparisons
- Keep code examples practical and production-ready

### 2. Content Guidelines
- **Architecture Descriptions**: Always explain both setup (ingestion) and usage (retrieval) phases
- **Diagrams**: Use Mermaid syntax with consistent styling per provider:
  - Azure: Blue theme (#0072C6)
  - AWS: Orange theme (#FF9900)
  - GCP: Blue theme (#4285F4)
- **Pricing Information**: Keep pricing data current and cite sources
- **Comparisons**: Be objective and highlight unique advantages of each provider

### 3. Code and Examples
- Provide complete, working examples
- Include error handling and best practices
- Comment complex sections
- Test all code snippets before including

### 4. File Operations
- Maintain existing file structure
- Use absolute paths for all file operations
- Preserve Markdown formatting and indentation
- Keep line lengths reasonable for readability

### 5. GitHub Pages Considerations
- Ensure all Markdown is GitHub-flavored
- Test Mermaid diagrams for GitHub rendering
- Verify all internal links work
- Keep assets in appropriate directories
- Optimize images for web viewing

### 6. CI/CD Workflow
- The project uses GitHub Actions to automatically publish to GitHub Pages
- Changes to main branch trigger automatic deployment
- Verify workflow runs successfully after changes

### 7. Version Control
- Make atomic commits with clear messages
- Branch naming: `feature/<description>`, `fix/<description>`, `docs/<description>`
- Keep pull requests focused on single concerns

### 8. When Making Changes
- Read existing content first to understand context
- Maintain consistent formatting and style
- Update table of contents if adding new sections
- Verify all links and references
- Test Mermaid diagrams locally when possible

## Common Tasks

### Adding New Cloud Provider
1. Add provider section to README.md
2. Create architecture diagram (Mermaid)
3. Update comparison tables
4. Add pricing information
5. Include usage examples

### Updating Pricing
1. Research current pricing from official sources
2. Update comparison table
3. Add note with last updated date
4. Verify calculations

### Adding Code Examples
1. Create examples in appropriate section
2. Test code for correctness
3. Add comments explaining key concepts
4. Include error handling

## Resources
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Mermaid.js Documentation](https://mermaid.js.org/)
- [GitHub Flavored Markdown](https://github.github.com/gfm/)
