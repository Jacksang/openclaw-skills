# Proposal/Quote HTML Template

The formal business proposal — this is the signable client artifact.

## Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{PROJECT} Platform — Project Proposal</title>
<style>
  :root {
    --p1: #2563eb; --p1bg: #eff6ff; --p1border: #bfdbfe;
    --p2: #7c3aed; --p2bg: #f5f3ff; --p2border: #ddd6fe;
    --p3: #64748b; --p3bg: #f8fafc; --p3border: #cbd5e1;
    --text: #1e293b; --muted: #64748b; --card: #fff; --bg: #f8fafc;
    --accent: #0891b2; --green: #16a34a;
  }
  /* Full CSS — use ELY PROPOSAL.html as reference implementation */
</style>
</head>
<body>
<div class="container">

  <!-- 1. COVER -->
  <div class="cover">
    <h1>{PROJECT} Platform — Project Proposal</h1>
    <div class="tagline">{One-line value proposition}</div>
    <div class="meta">
      <span>Version {X.Y}</span>
      <span>{Month Year}</span>
      <span>Confidential</span>
    </div>
  </div>

  <!-- 2. EXECUTIVE SUMMARY -->
  <div class="section">
    <h2>Executive Summary</h2>
    <div class="divider"></div>
    <p>{2-3 paragraph overview of the project, what it does, and the delivery approach}</p>
    <p>This proposal defines a **phased, low-risk delivery plan** starting with a focused MVP...</p>

    <div class="highlights">
      <div class="hl"><div class="num">{N}</div><div class="lbl">Weeks to MVP Launch</div></div>
      <div class="hl"><div class="num">${AMOUNT}</div><div class="lbl">Contracted MVP (SGD)</div></div>
      <div class="hl"><div class="num">{N}</div><div class="lbl">Future Growth Phases</div></div>
      <div class="hl"><div class="num">Lean</div><div class="lbl">MVP Delivery Model</div></div>
    </div>
  </div>

  <!-- 3. HOW IT WORKS -->
  <div class="section">
    <h2>How It Works — The {PROJECT} Journey</h2>
    <div class="divider"></div>
    <p>{Description of end-to-end workflow}</p>
    <div class="workflow">
      <div class="workflow-steps">
        <div class="workflow-step">📋 Step 1</div><span class="workflow-arrow">→</span>
        <div class="workflow-step">Step 2</div><span class="workflow-arrow">→</span>
        <div class="workflow-step">Step 3</div><span class="workflow-arrow">→</span>
        ...
      </div>
    </div>
  </div>

  <!-- 4. EXPECTED BUSINESS OUTCOMES (2-column grid) -->
  <div class="section">
    <h2>Expected Business Outcomes</h2>
    <div class="divider"></div>
    <div class="outcomes">
      <div class="outcome-item">
        <div class="title">{Icon} {Outcome}</div>
        <div class="desc">{How the platform delivers this}</div>
      </div>
      <!-- 4-6 outcome items -->
    </div>
  </div>

  <!-- 5. COMMITMENT SUMMARY — THE PRICING ANCHOR -->
  <div class="commitment-summary">
    <h3>📋 Delivery Commitment at a Glance</h3>
    <table>
      <tr><th>Phase</th><th>Commitment Level</th><th>Scope</th><th>Timeline</th><th>Investment</th></tr>
      <tr>
        <td>Phase 1 — Core MVP</td>
        <td><span class="label-contracted">CONTRACTED</span></td>
        <td>{Scope summary}</td>
        <td>{Weeks}</td>
        <td>${AMOUNT} SGD</td>
      </tr>
      <tr>
        <td>Phase 2 — Growth</td>
        <td><span class="label-conditional">CONDITIONAL</span></td>
        <td>{Scope summary}</td>
        <td>{Weeks}</td>
        <td>${AMOUNT} SGD</td>
      </tr>
      <tr>
        <td>Phase 3 — Future</td>
        <td><span class="label-roadmap">ROADMAP</span></td>
        <td>{Scope summary}</td>
        <td>—</td>
        <td>Not quoted</td>
      </tr>
      <tr class="total-row" style="background:rgba(52,211,153,0.15);">
        <td colspan="4" style="color:#ffffff;background:rgba(52,211,153,0.2);">Total Contracted + Conditional</td>
        <td style="color:#34d399;background:rgba(52,211,153,0.2);">${TOTAL} SGD</td>
      </tr>
    </table>
  </div>

  <!-- 6. PHASE 1 — CONTRACTED MVP (Detailed) -->
  <div class="section">
    <h2>🔵 PHASE 1 — Contracted MVP</h2>
    <!-- Scope box with feature bullet list -->
    <!-- Deliverables checklist -->
  </div>

  <!-- 7. PHASE 2 — CONDITIONAL GROWTH -->
  <div class="section">
    <h2>🟣 PHASE 2 — Conditional Growth Expansion</h2>
    <p style="color:#d97706;">⚠ Initiated once Phase 1 is generating revenue. Funded from platform profit.</p>
    <!-- Scope box -->
  </div>

  <!-- 8. PHASE 3 — FUTURE ROADMAP -->
  <div class="section">
    <h2>⚪ PHASE 3 — Future Innovation Roadmap</h2>
    <p style="color:var(--muted);">🔭 Strategic direction only — not committed.</p>
    <!-- Scope box -->
  </div>

  <!-- 9. DELIVERY TIMELINE -->
  <div class="section">
    <h2>📅 Delivery Timeline</h2>
    <table>
      <tr><th>Phase</th><th>Weeks</th><th>Key Activities</th><th>Checkpoint</th></tr>
      <!-- One row per phase -->
    </table>
  </div>

  <!-- 10. DELIVERY METHODOLOGY -->
  <div class="section">
    <h2>🔧 How We Deliver</h2>
    <div class="method">
      <div class="method-item">
        <div class="icon">🔄</div>
        <div class="mtitle">Two-Week Sprint Cycles</div>
        <div class="mdesc">{Description}</div>
      </div>
      <!-- 6 method items total: Sprints, Demos, UAT, Change Control, Bug SLAs, Warranty -->
    </div>
  </div>

  <!-- 11. INVESTMENT & PAYMENT TERMS -->
  <div class="section">
    <h2>💰 Investment & Payment Terms</h2>
    <h3>Phase 1 — Contracted MVP ($XX,XXX SGD)</h3>
    <table>
      <tr><th>Milestone</th><th>Amount</th><th>When</th></tr>
      <tr><td>Project Start</td><td>$X (30%)</td><td>Upon agreement signing</td></tr>
      <tr><td>Mid-Point Review</td><td>$X (40%)</td><td>Week N — after core features demoed</td></tr>
      <tr><td>MVP Go-Live</td><td>$X (30%)</td><td>Week N — after UAT signoff and launch</td></tr>
    </table>
    <h3>Phase 2 — Conditional Expansion ($XX,XXX SGD estimate)</h3>
    <p>Initiated when Phase 1 generates revenue. Payment terms negotiated at that time.</p>
    <h3>Phase 3 — Future Roadmap</h3>
    <p>Not quoted. Strategic partnership discussion after platform validation.</p>
  </div>

  <!-- 12. INCLUSIONS / EXCLUSIONS -->
  <div class="section">
    <h2>📋 What's Included — and What's Not</h2>
    <div class="assumptions" style="display:grid; grid-template-columns:1fr 1fr;">
      <div>
        <h4 style="color:var(--green);">✅ Included</h4>
        <ul>
          <li>All software design, development, and testing</li>
          <li>Cloud infrastructure configuration</li>
          <!-- ... -->
        </ul>
      </div>
      <div>
        <h4 style="color:#dc2626;">📌 Customer Responsibilities / Excluded</h4>
        <ul>
          <li>Cloud hosting costs (estimated $XX–YY/month)</li>
          <li>Third-party service fees</li>
          <!-- ... -->
        </ul>
      </div>
    </div>
  </div>

  <!-- 13. COST COMPARISON (Value Justification) -->
  <div class="section">
    <h2>📊 Why This Approach?</h2>
    <p>Traditional team of N people would need ~XX weeks and ~$X. Our approach achieves equivalent results at a fraction through modern development practices.</p>
    <div style="display:grid; grid-template-columns:1fr 1fr;">
      <div style="background:#f1f5f9;">Traditional: N people · ~X weeks · ~$X SGD</div>
      <div style="background:#f0fdf4;border:1px solid #bbf7d0;">Our Delivery: X weeks · ${AMOUNT} SGD · Contracted · Guaranteed</div>
    </div>
  </div>

  <div class="footer">
    {PROJECT} Platform · Project Proposal v{X.Y} · {Date} · Confidential<br>
    All prices in {Currency}. Phase 2 and 3 are conditional/roadmap — only Phase 1 is binding.
  </div>

</div>
</body>
</html>
```

## Commitment Label Design

```css
.label-contracted { background: #dcfce7; color: #16a34a; }  /* Green */
.label-conditional { background: #fef3c7; color: #d97706; } /* Amber */
.label-roadmap { background: #f1f5f9; color: #64748b; }     /* Grey */
```

## Total Row — Known CSS Issue

The global CSS rule `.total-row td { background: #f8fafc }` can conflict with inline `tr`-level styles applied via `style="background:rgba(...)"`. The fix: apply background colors directly to each `td` element in the total row using inline `style` attributes, NOT on the `tr`.

```html
<!-- WRONG — overridden by .total-row td -->
<tr class="total-row" style="background:rgba(52,211,153,0.15);">
  <td colspan="4">Total</td><td>${AMOUNT}</td>
</tr>

<!-- CORRECT — applied per td -->
<tr class="total-row">
  <td colspan="4" style="color:#ffffff;background:rgba(52,211,153,0.2);">Total</td>
  <td style="color:#34d399;background:rgba(52,211,153,0.2);">${AMOUNT}</td>
</tr>
```

## Pricing Communication Rules

- **Phase 1 is binding.** No weasel words. "Contracted" means guaranteed delivery at that price.
- **Phase 2 is conditional.** Explicitly state: "Initiated when Phase 1 generates revenue. Funded from platform profit."
- **Phase 3 is roadmap.** Not quoted. "Strategic direction only."
- **Total = Contracted + Conditional** shown prominently in the commitment summary.
- **Payment milestones:** 30% start + 40% midpoint + 30% go-live. Aligns client risk with delivery progress.
- **Cloud costs are EXCLUDED** from our price but estimated for the client's budget planning. Add buffer.
- **Hardware/third-party costs are EXCLUDED** and listed as customer responsibilities.
