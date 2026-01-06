# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a demo website for "Esimerkki Kiinteistöhuolto Oy" (Example Property Maintenance Ltd) featuring an integrated AI chatbot. The project demonstrates a **constrained, safe internal AI chat** system that only answers questions based on company-specific information.

**Critical Design Principle**: This is NOT a general-purpose chatbot. It's a bounded knowledge system that strictly adheres to predefined company information and refuses to answer questions outside its knowledge base.

## File Structure

- **index.html** - Main landing page with integrated floating chat widget
- **ai-chatbox.html** - Standalone full-screen chat interface (demo version)
- **claudelle_rakennuohjeet_ai-chatboxille.txt** - Original requirements specification (in Finnish)
- **README.md** - User-facing documentation

## How to Test/Run

Simply open the HTML files in a web browser:
- `index.html` - Recommended. Chat appears as floating widget in bottom-right corner
- `ai-chatbox.html` - Full-screen chat interface

No build process, server, or dependencies required. Everything is self-contained in single HTML files.

## Architecture

### Knowledge Base Structure

Both HTML files contain identical `companyKnowledgeBase` JavaScript object:

```javascript
const companyKnowledgeBase = {
    yritys: "Esimerkki Kiinteistöhuolto Oy",
    aukioloajat: {
        arkipaivat: "Ma–Pe klo 8–16",
        paivystys: "Päivystys vain kiireellisissä vioissa"
    },
    palvelut: [
        "Kiinteistöhuolto",
        "Pienkorjaukset",
        "Vikailmoitusten vastaanotto"
    ],
    yhteystiedot: {
        sahkoposti: "huolto@esimerkki.fi",
        puhelin: "040 123 4567"
    },
    tyoohjeet: [
        "Vikailmoitukset kirjataan sähköpostiin",
        "Kiireelliset tapaukset ohjataan päivystykseen",
        "Asiakkaille ei luvata aikatauluja ilman esimiehen lupaa"
    ]
}
```

### AI Response Logic (`getAIResponse` function)

The chatbot uses **keyword-based pattern matching** (not LLM calls) to map user questions to knowledge base responses:

1. **Input normalization**: Converts user message to lowercase
2. **Pattern matching**: Checks for keywords using `message.includes()`
3. **Strict boundaries**: Returns predefined responses from knowledge base only
4. **Fallback**: If no match found, returns: "En löydä vastausta ohjeista. Ole yhteydessä esimieheen tai asiakaspalveluun."

**Response categories handled**:
- Aukioloajat (opening hours)
- Palvelut (services)
- Yhteystiedot (contact info)
- Vikailmoitukset (fault reports)
- Päivystys (emergency service)
- Aikataulut (schedules)
- Työohjeet (work instructions)
- Tervehdykset (greetings)
- Kiitokset (thanks)

### Key Constraints (from requirements)

When modifying the chatbot logic, **strictly follow these rules**:

1. **No external data**: Only use information from `companyKnowledgeBase`
2. **No guessing**: Never fabricate or extrapolate answers
3. **Clear fallback**: Unknown questions → direct user to supervisor/customer service
4. **Professional tone**: Clear, calm, professional Finnish language
5. **No human impersonation**: Never claim to be human
6. **Contact info completeness**: When mentioning contact methods for faults/emergencies, include BOTH email AND phone number (040 123 4567)

## Making Changes

### Updating Company Information

Edit the `companyKnowledgeBase` object in **both files** (index.html and ai-chatbox.html):

```javascript
yhteystiedot: {
    sahkoposti: "new@email.fi",  // Update here
    puhelin: "040 XXX XXXX"      // And here
}
```

**Important**: Keep both files in sync when updating knowledge base.

### Adding New Response Patterns

Add new `if` conditions in `getAIResponse()` function:

```javascript
if (message.includes('keyword1') || message.includes('keyword2')) {
    return `Response using ${companyKnowledgeBase.field}`;
}
```

Place more specific patterns before general ones to avoid false matches.

### Styling Changes

Both files use embedded CSS with:
- **Color scheme**: Purple-blue gradient (`#667eea` → `#764ba2`)
- **Font**: 'Segoe UI' and fallbacks
- **Responsive breakpoint**: `@media (max-width: 768px)`

Key CSS classes:
- `.chat-messages` - Scrollable message container
- `.message.user` / `.message.bot` - Message bubbles
- `.typing-indicator` - Three-dot animation during "thinking"
- `.chat-button` - Floating action button (index.html only)
- `.chat-window` - Chat interface container

## Landing Page Structure (index.html)

The page follows a single-page design with these sections:

1. **Header** - Fixed navigation bar with smooth scroll links
2. **Hero** - Gradient background with CTA button
3. **Palvelut** (#palvelut) - Three service cards with hover effects
4. **Aukioloajat** (#aukioloajat) - Opening hours display
5. **Yhteystiedot** (#yhteystiedot) - Contact cards with clickable links
6. **Footer** - Copyright info
7. **Chat Widget** - Floating button + popup window

Navigation links use anchor links (#palvelut, #aukioloajat, #yhteystiedot) with smooth scroll behavior.

## Common Modifications

### Changing Chat Position (index.html)

The floating chat widget is positioned with:
```css
.chat-widget {
    position: fixed;
    bottom: 20px;
    right: 20px;
}
```

### Adjusting Response Delay

Simulated "thinking" time in `sendMessage()` / `sendChatMessage()`:
```javascript
setTimeout(() => {
    // Response logic
}, 800 + Math.random() * 400);  // 800-1200ms delay
```

### Mobile Responsiveness

Both files are responsive. Key mobile adjustments at 768px breakpoint:
- Chat window becomes full-width minus margins
- Hero text sizes reduce
- Navigation hides (index.html)

## Language

All UI text, comments, and responses are in **Finnish**. The target audience is Finnish-speaking employees.

## Testing Checklist

When making changes, verify:
- [ ] Contact info (email + phone) appears in vikailmoitus and päivystys responses
- [ ] Unknown questions trigger fallback message correctly
- [ ] No responses reference external/unspecified information
- [ ] Typing indicator appears and disappears correctly
- [ ] Mobile view works (test at < 768px width)
- [ ] Both index.html and ai-chatbox.html remain in sync (if updating chat logic)
- [ ] Smooth scroll works on landing page navigation
- [ ] All links (email, phone) are clickable and correct
