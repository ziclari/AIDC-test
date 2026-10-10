# AnyCompanyRead - User Personas

**Version:** 1.0  
**Date:** October 9, 2026  
**AI-DLC Phase:** User Stories Definition

---

## Overview

This document defines the user personas for AnyCompanyRead, derived from the approved requirements and project context. Four distinct personas are identified based on the customer research, each with unique characteristics, goals, and pain points.

**Source References:**
- `project-context/03-customer-research.md` - Customer segments and research findings
- `aidlc-docs/requirements.md` - Business and functional requirements
- `aidlc-docs/requirement-verification-questions.md` - Clarification decisions D-007, D-008

---

## Persona 1: Avid Reader

### Profile
| Field | Value |
|-------|-------|
| Name | Sarah Mitchell |
| Age | 38 |
| Occupation | Marketing Manager |
| Income | $85K |
| Location | Toronto, Canada |
| Reads | 25+ books/year |
| Preferred Formats | Physical books, e-books |

### Background
Sarah is an avid reader who discovered new books through social media, book blogs, and recommendations from friends. She values discovery and quality curation over sheer volume. She shops for books weekly and enjoys both physical and digital formats depending on context.

### Goals
1. **Discover new books and authors** - Find books that match her interests without irrelevant recommendations
2. **Access content across devices** - Read seamlessly between physical, e-book, and audiobook formats
3. **Connect with other readers** - Participate in book clubs and discussions
4. **Build a personalized library** - Maintain reading history and preferences

### Pain Points
- "Too many irrelevant recommendations" (68% of survey respondents cited this)
- "Difficult to discover new authors" (54% of survey respondents cited this)
- "Limited community features" (38% of survey respondents cited this)

### Behavior Patterns
- Uses mobile app for browsing during commutes
- Purchases physical books for her home library
- Uses e-books for travel
- Follows authors on social media for updates

---

## Persona 2: Student & Academic

### Profile
| Field | Value |
|-------|-------|
| Name | Emily Rodriguez |
| Age | 22 |
| Occupation | Graduate Student (History) |
| Income | $25K (part-time work + loans) |
| Location | Chicago, USA |
| Reads | 15+ books/year (academic + personal) |
| Preferred Formats | E-books, used physical books |

### Background
Emily is a graduate student who needs textbooks and academic resources for her studies, plus personal reading for relaxation. She's highly price-sensitive and shops for both new and used books. She values affordability and digital access.

### Goals
1. **Find affordable textbooks and academic resources** - Locate required materials at the lowest price
2. **Access content immediately** - Get digital access to textbooks for urgent study needs
3. **Resell or archive materials** - Manage book lifecycle after courses end
4. **Organize academic reading** - Keep track of research materials and citations

### Pain Points
- "Expensive shipping costs" (35% of survey respondents cited this)
- "Poor mobile experience" (42% of survey respondents cited this)
- High textbook costs compared to alternatives

### Behavior Patterns
- Searches by ISBN for exact textbook matches
- Compares new vs. used prices before purchasing
- Uses mobile app for quick ISBN scans in bookstore
- Prefers e-books for immediate access to textbooks

---

## Persona 3: Professional Development

### Profile
| Field | Value |
|-------|-------|
| Name | Michael Chen |
| Age | 42 |
| Occupation | Senior Engineering Manager |
| Income | $155K |
| Location | Austin, USA |
| Reads | 12+ books/year (business + self-improvement) |
| Preferred Formats | Audiobooks, e-books |

### Background
Michael is a busy professional who uses reading for career development and personal growth. He values convenience, recommendations based on his interests, and time efficiency. He prefers audiobooks for commuting and e-books for quick reference.

### Goals
1. **Find career-relevant content quickly** - Discover business and self-improvement books that match his professional needs
2. **Learn on-the-go** - consume content during commute and downtime
3. **Track professional reading** - Maintain a record of books consumed for learning
4. **Access content from multiple devices** - Seamlessly switch between work and personal devices

### Pain Points
- "Checkout process too complicated" (47% of survey respondents cited this)
- "Time efficiency" is his top value
- Limited access to audiobooks on current platforms

### Behavior Patterns
- Uses audiobooks for 100% of commute reading
- Purchases business books within 24 hours of recommendation
- Uses "one-click reorder" for previously purchased formats
- Prefers 1-click purchasing for impulse buys

---

## Persona 4: Gift Buyer

### Profile
| Field | Value |
|-------|-------|
| Name | Jennifer Thompson |
| Age | 52 |
| Occupation | Non-profit Director |
| Income | $95K |
| Location | Seattle, USA |
| Reads | 8+ books/year (mostly gifts) |
| Preferred Formats | Physical books with gift wrapping |

### Background
Jennifer frequently buys books as gifts for birthdays, holidays, and special occasions. She values presentation, personalization, and reliability. She's less concerned with price and more focused on finding the right book for the recipient.

### Goals
1. **Find the perfect gift** - Discover books that match recipients' interests
2. **Personalize gifts** - Add personal messages and gift wrapping
3. **Ensure timely delivery** - Get books delivered by specific dates
4. **Create gift subscriptions** - Set up recurring book gifts for loved ones

### Pain Points
- "Too many irrelevant recommendations" affects gift buying
- "Presentation and personalization" are key concerns
- Limited gift-specific features on current platforms

### Behavior Patterns
- Creates gift registries for different recipients
- Uses "gift subscription" feature regularly
- Prefers physical books for gifting
- Schedules gift orders 1-2 weeks in advance for holidays

---

## Persona Comparison Matrix

| Aspect | Avid Reader | Student & Academic | Professional Development | Gift Buyer |
|--------|-------------|-------------------|--------------------------|------------|
| **Primary Goal** | Discovery & community | Affordability & access | Time efficiency | Gift perfection |
| **Format Preference** | Mixed (physical, e-book) | E-books, used physical | Audiobooks, e-books | Physical only |
| **Price Sensitivity** | Medium | High | Low | Low |
| **Purchase Frequency** | Weekly | Monthly | Bi-weekly | Occasional |
| **Mobile Usage** | High | High | Very High | Medium |
| **Key Pain Point** | Irrelevant recommendations | Shipping costs | Complicated checkout | Presentation |

---

## Personas and Requirements Traceability

| Persona | Related Business Requirements | Related Functional Requirements |
|---------|------------------------------|--------------------------------|
| Avid Reader | BR-01 (Book Sales Platform) | FR-01, FR-02, FR-03, FR-04, FR-05, FR-11, FR-12 |
| Student & Academic | BR-01, BR-07 | FR-01, FR-02, FR-03, FR-04, FR-05, FR-10 |
| Professional Development | BR-01, BR-07 | FR-01, FR-02, FR-03, FR-04, FR-05, FR-09, FR-10 |
| Gift Buyer | BR-01, BR-07 | FR-01, FR-02, FR-04, FR-05, FR-10 |

---

## Personas and User Goals Traceability

| Persona | User Goals | Related User Stories |
|---------|-----------|---------------------|
| Avid Reader | Discover new books, Access content across devices, Connect with readers, Build personalized library | Discovery, Access, Community stories |
| Student & Academic | Find affordable textbooks, Access content immediately, Resell/archive materials, Organize academic reading | Search, Checkout, Account stories |
| Professional Development | Find career-relevant content, Learn on-the-go, Track professional reading, Access from multiple devices | Search, Checkout, Digital delivery stories |
| Gift Buyer | Find perfect gift, Personalize gifts, Ensure timely delivery, Create gift subscriptions | Search, Checkout, Account stories |

---

*End of User Personas Document*
