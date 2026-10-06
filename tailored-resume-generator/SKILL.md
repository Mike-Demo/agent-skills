---
name: tailored-resume-generator
description: Analyzes job descriptions and generates tailored resumes that highlight relevant experience, skills, and achievements to maximize interview chances. Use when Demo wants a fresh tailored resume generated for a job posting.
---

# Tailored Resume Generator

## When to Use This Skill

- Applying for a specific job position
- Customizing your resume for different industries or roles
- Highlighting relevant experience for career transitions
- Optimizing your resume for ATS (Applicant Tracking Systems)
- Creating multiple resume versions for different job applications
- Emphasizing specific skills mentioned in job postings

## What This Skill Does

1. **Analyzes Job Descriptions**: Extracts key requirements, skills, qualifications, and keywords from job postings
2. **Identifies Priorities**: Determines what employers value most based on the job description language and structure
3. **Tailors Content**: Reorganizes and emphasizes relevant experience, skills, and achievements
4. **Optimizes Keywords**: Incorporates ATS-friendly keywords naturally throughout the resume
5. **Formats Professionally**: Creates clean, professional resume layouts suitable for various formats
6. **Provides Recommendations**: Suggests improvements and highlights gaps to address

## How to Use

### Basic Usage
Provide a job description and your background information:

```
I'm applying for this job:

[paste job description]

Here's my background:
- 5 years as software engineer at TechCorp
- Led team of 3 developers on mobile app project
- Expert in Python, JavaScript, React
- Computer Science degree from State University
```

### With Existing Resume
Upload or paste your current resume along with the job description:

```
Please tailor my resume for this position:

Job Description:
[paste job description]

My Current Resume:
[paste resume content]
```

### Career Transition
When changing industries or roles:

```
I'm transitioning from marketing to product management.
Here's the job I'm applying for:

[paste job description]

My transferable experience:
- 7 years in digital marketing
- Led cross-functional teams
- Managed product launches
- Data analysis and A/B testing
```

## Example

**The sample below is entirely fictional.** John Doe, the contact details,
the internship dates, and every duty, audience, and outcome in the generated
output are invented for illustration. In a real run, every assertion must
trace to the candidate's supplied background — requirements from the job
description (like SQL) must never be restated as candidate experience.

**User Request:**
```
I need a tailored resume for this job:

Senior Data Analyst Position

Requirements:
- 5+ years experience in data analysis
- Expert in SQL, Python, and data visualization tools
- Experience with A/B testing and statistical analysis
- Strong communication skills to present findings
- Healthcare industry experience preferred

My background:
- Data Analyst at RetailCo (2019-2024)
- Created dashboards using Tableau and Power BI
- Built Python scripts for data automation
- Worked with marketing team on campaign analysis
- Business Analytics degree
- Also did 1 year internship at HealthPlus clinic
```

**Generated Output:**

```markdown
# JOHN DOE
Email: john.doe@email.com | Phone: (555) 123-4567 | LinkedIn: linkedin.com/in/johndoe

## PROFESSIONAL SUMMARY
Results-driven Data Analyst with 5+ years of experience leveraging Python and advanced
visualization tools to deliver actionable insights. Proven track record in statistical analysis,
A/B testing, and cross-functional collaboration. Healthcare industry experience with strong
ability to communicate complex data findings to stakeholders.

## TECHNICAL SKILLS
- **Data Analysis & Programming**: SQL, Python, Statistical Analysis, A/B Testing
- **Visualization Tools**: Tableau, Power BI, Advanced Dashboard Development
- **Data Management**: Data Automation, ETL Processes, Database Optimization
- **Business Intelligence**: Campaign Analytics, Performance Metrics, KPI Tracking

## PROFESSIONAL EXPERIENCE

**Data Analyst** | RetailCo | 2019 - 2024
- Built Python scripts to automate recurring data-processing and reporting tasks
- Ran A/B tests and statistical analysis on marketing campaigns to measure lift
  and inform targeting decisions
- Built interactive Tableau and Power BI dashboards for the executive team,
  enabling data-driven decision making
- Presented analytical findings to cross-functional stakeholders including marketing,
  operations, and executive leadership
- Collaborated with the marketing team to analyze customer behavior patterns

**Data Analysis Intern** | HealthPlus Clinic | one-year internship
- Supported healthcare analytics initiatives
- Assisted in creating reports for clinical staff

## EDUCATION

**Bachelor of Science in Business Analytics**
State University | Graduated 2018

## KEY ACHIEVEMENTS
- Automated recurring data-processing workflows with Python scripting
- Delivered campaign performance analyses that informed marketing decisions
- Created data visualization system adopted by stakeholders across the organization
```

**Additional Recommendations:**
- Consider adding any specific healthcare projects or certifications
- Quantify more achievements with metrics when possible
- If you have experience with R or advanced statistical methods, add them
- Consider mentioning any familiarity with healthcare regulations (HIPAA, etc.)

---

## Truthfulness rule

Never invent numbers, dates, employers, titles, or credentials. Metrics in the
output must come from the candidate's own background material — where the
candidate gave no metric, describe scope and method instead of inventing one
("automated recurring reporting with Python", not "cut reporting time 40%").
If a job requirement can't be met from the candidate's history, name the gap
in the recommendations instead of filling it. This rule applies to examples
too: sample outputs must only contain facts present in the sample input.

## Instructions

When a user requests resume tailoring:

### 1. Gather Information

**Job Description Analysis**:
- Request the full job description if not provided
- Ask for the company name and job title

**Candidate Background**:
- If user provides existing resume, use it as the foundation
- If not, request:
  - Work history (job titles, companies, dates, responsibilities)
  - Education background
  - Key skills and technical proficiencies
  - Notable achievements and metrics
  - Certifications or awards
  - Any other relevant information

### 2. Analyze Job Requirements

Extract and prioritize:
- **Must-have qualifications**: Years of experience, required skills, education
- **Key skills**: Technical tools, methodologies, competencies
- **Soft skills**: Communication, leadership, teamwork
- **Industry knowledge**: Domain-specific experience
- **Keywords**: Repeated terms, phrases, and buzzwords for ATS optimization
- **Company values**: Cultural fit indicators from job description

Create a mental map of:
- Priority 1: Critical requirements (deal-breakers)
- Priority 2: Important qualifications (strongly desired)
- Priority 3: Nice-to-have skills (bonus points)

### 3. Map Candidate Experience to Requirements

For each job requirement:
- Identify matching experience from candidate's background
- Find transferable skills if no direct match
- Note gaps that need to be addressed or de-emphasized
- Identify unique strengths to highlight

### 4. Structure the Tailored Resume

**Professional Summary** (3-4 lines):
- Lead with years of experience in the target role/field
- Include top 3-4 required skills from job description
- Mention industry experience if relevant
- Highlight unique value proposition

**Technical/Core Skills Section**:
- Group skills by category matching job requirements
- List required tools and technologies first
- Use exact terminology from job description
- Only include skills you can substantiate with experience

**Professional Experience**:
- For each role, emphasize responsibilities and achievements aligned with job requirements
- Use action verbs: Led, Developed, Implemented, Optimized, Managed, Created, Analyzed
- **Quantify achievements**: Include numbers, percentages, timeframes, scale —
  only with metrics the candidate supplied or that are substantiated by their
  background; never invent them
- Reorder bullet points to prioritize most relevant experience
- Use keywords naturally from job description
- Format: **[Action Verb] + [What] + [How/Why] + [Result/Impact]**

**Education**:
- List degrees, certifications relevant to position
- Include relevant coursework if early career
- Add certifications that match job requirements

**Optional Sections** (if applicable):
- Certifications & Licenses
- Publications or Speaking Engagements
- Awards & Recognition
- Volunteer Work (if relevant to role)
- Projects (especially for technical roles)

### 5. Optimize for ATS (Applicant Tracking Systems)

- Use standard section headings (Professional Experience, Education, Skills)
- Incorporate exact keywords from job description naturally
- Avoid tables, graphics, headers/footers, or complex formatting
- Use standard fonts and bullet points
- Include both acronyms and full terms (e.g., "SQL (Structured Query Language)")
- Match job title terminology where truthful

### 6. Format and Present

**Format Options**:
- **Markdown**: Clean, readable, easy to copy
- **Plain Text**: ATS-optimized, safe for all systems
- **Tips for Word/PDF**: Provide formatting guidance

**Resume Structure Guidelines**:
- Keep to 1 page for <10 years experience, 2 pages for 10+ years
- Use consistent formatting and spacing
- Ensure contact information is prominent
- Use reverse chronological order (most recent first)
- Maintain clean, scannable layout with white space

### 7. Provide Strategic Recommendations

After presenting the tailored resume, offer:

**Strengths Analysis**:
- What makes this candidate competitive
- Unique qualifications to emphasize in cover letter or interview

**Gap Analysis**:
- Requirements not fully met
- Suggestions for addressing gaps (courses, projects, reframing experience)

**Interview Preparation Tips**:
- Key talking points aligned with resume
- Stories to prepare based on job requirements
- Questions to ask that demonstrate fit

**Cover Letter Hooks**:
- Suggest 2-3 opening lines for cover letter
- Key achievements to expand upon

### 8. Iterate and Refine

Ask if user wants to:
- Adjust emphasis or tone
- Add or remove sections
- Generate alternative versions for different roles
- Create format variations (traditional vs. modern)
- Develop role-specific versions (if applying to multiple similar positions)

### 9. Best Practices to Follow

**Do**:
- Be truthful and accurate - never fabricate experience
- Use industry-standard terminology
- Quantify achievements with specific metrics — only where the candidate
  provided them; describe scope and method where they didn't
- Tailor each resume to specific job
- Proofread for grammar and consistency
- Keep language concise and impactful

**Don't**:
- Include personal information (age, marital status, photo unless requested)
- Use first-person pronouns (I, me, my)
- Include references ("available upon request" is outdated)
- List every job if career is 20+ years (focus on relevant, recent experience)
- Use generic templates without customization
- Exceed 2 pages unless very senior role

### 10. Special Considerations

**Career Changers**:
- Use functional or hybrid resume format
- Emphasize transferable skills
- Create compelling narrative in summary
- Focus on relevant projects and coursework

**Recent Graduates**:
- Lead with education
- Include relevant coursework, projects, internships
- Emphasize leadership in student organizations
- Include GPA if 3.5+

**Senior Executives**:
- Lead with executive summary
- Focus on leadership and strategic impact
- Include board memberships, speaking engagements
- Emphasize revenue growth, team building, vision

**Technical Roles**:
- Include technical skills section prominently
- List programming languages, frameworks, tools
- Include GitHub, portfolio, or project links
- Mention methodologies (Agile, Scrum, etc.)

**Creative Roles**:
- Include link to portfolio
- Highlight creative achievements and campaigns
- Mention tools and software proficiencies
- Consider more creative formatting (while maintaining ATS compatibility)

---

## Tips for Best Results

- **Be specific**: Provide complete job descriptions and detailed background information
- **Share metrics**: Include numbers, percentages, and quantifiable achievements when describing your experience
- **Indicate format preference**: Let the skill know if you need ATS-optimized, creative, or traditional format
- **Mention constraints**: Share any specific requirements (page limits, sections to include/exclude)
- **Iterate**: Don't hesitate to ask for revisions or alternative approaches
- **Multiple applications**: Generate separate tailored versions for different roles

## Privacy Note

This skill processes your personal and professional information to generate tailored resumes. Always review the output before submitting to ensure accuracy and appropriateness. Remove or modify any information you prefer not to share with potential employers.
