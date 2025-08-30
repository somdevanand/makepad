# Documentation Style Guide

## Overview
This style guide establishes consistent formatting and writing conventions for Makepad documentation. Following these guidelines ensures clarity, consistency, and professionalism across all documentation.

## Writing Style

### Voice and Tone
- Use active voice whenever possible
- Maintain a professional but approachable tone
- Be concise and clear
- Avoid jargon unless necessary, and explain technical terms when first introduced

### Grammar and Mechanics
- Use American English spelling and grammar
- Use serial commas (Oxford commas)
- Use present tense for describing functionality
- Use second person ("you") when addressing the reader
- Use first person plural ("we") when referring to the Makepad team or community

### Sentence Structure
- Keep sentences short and focused
- Break up complex ideas into multiple sentences
- Use bullet points and numbered lists for sequential or related information
- Vary sentence length to maintain reader engagement

## Formatting Guidelines

### Headings
- Use sentence case for headings (only capitalize the first word and proper nouns)
- Use H1 (#) for main page titles
- Use H2 (##) for section headings
- Use H3 (###) for subsection headings
- Avoid H4-H6 headings unless absolutely necessary

### Code Formatting
- Use inline code formatting (backticks) for:
  - Function names: `draw_quad()`
  - Variable names: `counter`
  - File names: `main.rs`
  - Commands: `cargo build`
- Use code blocks (triple backticks) for:
  - Code examples
  - File contents
  - Command sequences
- Specify the language for code blocks when appropriate:
  ```rust
  fn main() {
      println!("Hello, Makepad!");
  }
  ```

### Links
- Use descriptive link text rather than generic text like "click here"
- Link to relevant sections within the documentation
- Link to external resources when appropriate
- Use relative links for internal documentation references

### Lists
- Use bulleted lists for unordered items
- Use numbered lists for sequential steps or ordered information
- Keep list items parallel in structure
- Use consistent punctuation at the end of list items (usually no punctuation for single phrases, complete sentences get periods)

### Emphasis
- Use bold (`**bold**`) sparingly for important terms or UI elements
- Use italics (`*italic*`) for book titles, new terms, or emphasis
- Avoid underlining (it can be confused with hyperlinks)

## Content Organization

### Page Structure
1. **Overview**: Brief introduction to the topic
2. **Main Content**: Detailed information, organized by subheadings
3. **Examples**: Practical examples demonstrating concepts
4. **Related Resources**: Links to related documentation or external resources
5. **Troubleshooting**: Common issues and solutions (when applicable)

### Examples and Code Snippets
- Provide complete, working examples whenever possible
- Include comments in code examples to explain key concepts
- Show both correct and incorrect usage when relevant
- Use realistic, practical examples that users can relate to
- Indicate when code is conceptual vs. production-ready

### API Documentation
- Document all public functions, structs, and traits
- Include a brief description of what each item does
- List parameters with their types and descriptions
- Provide return value information
- Include examples of usage
- Document any errors or panics that might occur

## Visual Elements

### Images and Diagrams
- Use images and diagrams to illustrate complex concepts
- Ensure images are high-quality and appropriately sized
- Provide alt text for accessibility
- Use consistent styling for diagrams

### Tables
- Use tables for comparing options or displaying structured data
- Keep tables simple and easy to read
- Use header rows to describe columns
- Align content appropriately within cells

## Terminology

### Consistent Terms
- Use "Makepad" consistently (not "makepad" or "MakePad")
- Use "Rust" for the programming language
- Use "application" rather than "app" in formal documentation
- Use "library" rather than "crate" in user-facing documentation

### Technical Terms
- Define technical terms when first introduced
- Use established terminology from the Rust ecosystem
- Be consistent with naming conventions across the documentation

## Accessibility

### Writing for Accessibility
- Use clear, simple language
- Provide context for abbreviations and acronyms
- Use descriptive link text
- Structure content with proper headings
- Avoid color as the sole means of conveying information

### Code Examples
- Ensure code examples are accessible to screen readers
- Provide text descriptions of visual elements in code
- Use semantic HTML when possible in web examples

## Versioning and Updates

### Keeping Documentation Current
- Update documentation when features change
- Mark deprecated features clearly
- Include version information for features that were added or changed
- Review and update documentation regularly

### Change Logs
- Maintain change logs for significant documentation updates
- Include the date and version of changes
- Briefly describe what was changed and why

## Review and Quality Assurance

### Review Process
- All documentation should be reviewed by at least one other team member
- Check for technical accuracy
- Verify that examples work as described
- Ensure consistency with style guide

### Testing Examples
- Test all code examples to ensure they work correctly
- Verify that commands produce the expected output
- Check that links are valid and point to the correct destinations

By following this style guide, we can create documentation that is clear, consistent, and helpful to users of all experience levels.