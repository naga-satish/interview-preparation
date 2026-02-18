# GitHub Copilot PR Review Instructions

When reviewing pull requests in this repository, please provide:

## 📝 What Changed (Plain English Summary)
- List each file changed and explain what was modified in simple, non-technical terms
- Explain the purpose and goal of these changes
- Describe what functionality is being added, improved, or fixed

## 🎯 What This Implements
- Explain what problem this solves or what need it addresses
- Describe the new features, improvements, or bug fixes
- Mention any new capabilities or enhancements added to the codebase

---

## 🔍 Detailed Review Feedback

### For Python Files (.py)
Review for:
- **Code Quality**: PEP 8 compliance, readability, maintainability
- **Bugs**: Potential bugs, edge cases not handled, logic errors
- **Performance**: Inefficient code, unnecessary operations, optimization opportunities
- **Security**: SQL injection, command injection, insecure operations
- **Error Handling**: Missing try-catch blocks, unhandled exceptions
- **Documentation**: Missing docstrings, unclear variable names, lack of comments
- **Type Hints**: Missing or incorrect type annotations
- **Testing**: Untested code paths, missing test cases

### For Markdown Files (.md)
Review for:
- **Formatting Standards** (per Claude.md):
  - Answers must start with `**Answer:**` prefix
  - One blank line after each question
  - No indentation (standard markdown)
  - Answer length: 50-200 words for interview prep
- **Clarity**: Unclear explanations, confusing wording, poor structure
- **Examples**: Missing code examples, lack of practical demonstrations
- **Technical Accuracy**: Incorrect information, outdated references
- **Completeness**: Incomplete explanations, missing key points

### For Config/YAML Files
Review for:
- **Syntax**: YAML syntax errors, incorrect formatting
- **Security**: Exposed secrets, insecure configurations
- **Validity**: Invalid configuration options, missing required fields
- **Best Practices**: Suboptimal settings, deprecated options

### For All Files
Review for:
- **Repository Standards**: Adherence to project conventions
- **Maintainability**: Code/content that's hard to maintain or understand
- **Organization**: Poor file structure, misplaced code/content

---

## 📋 Review Guidelines

- Use **clear, plain English** for explanations
- Provide **specific line numbers** with feedback
- Be **constructive and helpful**, not critical
- Suggest **concrete improvements** with examples
- Prioritize **high-impact issues** over minor style points
- Explain **why** something is an issue, not just what

---

*These instructions guide automatic Copilot reviews for all pull requests.*
