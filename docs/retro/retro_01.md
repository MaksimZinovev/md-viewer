# Agile Retrospective: Retro Note v5

**Project:** Plugin MVP · **Date:** 2026-06-14 · **Participants:** Max, Colleague · **Duration:** ~45 min

---



---

## Launch Timeline & Scope
**TL;DR:** Launch delayed by 2 weeks due to communication gaps and initial scope creep; narrowed MVP was the right call.

<!-- alt: Timeline showing project start in April, May 1st target, and eventual launch delay. -->
```mermaid
%%{init: {"theme": "base", "themeVariables": {"primaryColor": "#3B7DC4", "primaryTextColor": "#ffffff", "secondaryColor": "#D4760A", "secondaryTextColor": "#ffffff", "tertiaryColor": "#E8E8E8", "tertiaryTextColor": "#1a1a1a", "lineColor": "#333"}}}%%

timeline
    title Project Launch Roadmap
    section Q2 2026
        April : Project Start : MVP Definition
        May 1 : Target Launch : "Published/Launched" goal
        Mid-May : Delay : Missing emails : Monitoring ticket status
        Late May : Actual Launch : Narrowed Scope
```

<details>
<summary>Details</summary>

*   **Scope Management:** Initially took on too much; narrowing functional scope saved the project from further stretching.
*   **Bottlenecks:** A week was lost because an email notification wasn't seen; learned to monitor the URL/ticket status directly.
*   **Complexity:** Launching in a "closed ecosystem" (Atlassian/Marketplaces) revealed more hurdles than expected (Advanced/Standard versions).
</details>

---

## Development Workflow & CI/CD
**TL;DR:** Debate over running tests on every 'push' to dev vs. 'merge' to master; consensus on automation over manual review.

<!-- alt: Flowchart of the proposed CI/CD pipeline from push to deploy. -->
```mermaid
flowchart LR
    A["Push to Dev"] --> B{"Run Tests?"}
    B -- "Current" --> C["No (Manual)"]
    B -- "Proposed" --> D["AI Slop/Inspect"]
    D --> E["Merge to Master"]
    E --> F["Automated Regression"]
    F --> G["Deploy"]
    style D fill:#D4760A,color:#ffffff
```

<details>
<summary>Details</summary>

*   **Test Frequency:** Arguments for tests on 'push' to see failures early; counter-argument that too many dependencies slow down development.
*   **AI Tooling:** Currently using `AI Slop` (pattern analysis) and `Inspect` (AI-driven code review via API).
*   **Self-Hosting:** Successfully using Tailscale for shared access and self-hosted server for history tracking.
</details>

---

## Security & Reliability
**TL;DR:** Shift toward "MFA everywhere" and automated secret scanning to prevent token leaks.

| Strategy | Tool/Method | Goal |
| :--- | :--- | :--- |
| **Secret Scanning** | `GitLeaks` | Block commits containing keys |
| **Access Control** | `MFA` | Multi-factor for all accounts |
| **Token Safety** | Rotation | Periodic rotation of all prod keys |

<details>
<summary>Details</summary>

*   **Automation:** "Don't include the brain" in repetitive security tasks—automate it so it's on "auto-pilot."
*   **Local Hooks:** Using pre-commit/pre-push hooks for linting and security scanning to catch issues before they hit GitHub.
</details>

---

## Time Management & Balance
**TL;DR:** Team significantly exceeded the 4h/week commitment, sometimes hitting 4h/day; need for better time-boxing.

<!-- alt: Radar chart showing the balance between planned and actual time usage. -->
```mermaid
radar-beta
    title Effort vs. Balance
    axis t["Time Spent"], b["Work-Life Balance"], e["Efficiency"], o["Focus on MVP"], s["Skill Building"]
    curve team["Actual"]{5, 2, 4, 3, 5}
    curve plan["Planned"]{2, 5, 3, 5, 2}
```

<details>
<summary>Details</summary>

*   **Underestimation:** Practice showed effort was several times higher than abstract estimates.
*   **"Skill vs. Project":** Often spending time building reusable "skills" or universal tools rather than the project itself.
*   **Burnout Risk:** Late nights (until 10 PM) aren't efficient; necessity to apply professional time-management to personal projects.
</details>

---

## Ideas
**TL;DR:** Brainstormed improvements for tracking and maintaining the codebase.

<!-- alt: Mindmap of ideas for future retros. -->
```mermaid
mindmap
  root((Potential Ideas))
    Time Tracking
      Find/Build simple Mac tool
      Track real vs perceived effort
    Maintenance
      Monthly 5% refactor task
      Automated AI review in CI/CD
    Security
      Automatic token rotation
```

<details>
<summary>Details</summary>

*   **Refactoring:** Allocate specific time (e.g., 5% or one task a month) for "Refactoring/Security" review rather than doing it ad-hoc.
*   **Tracker:** Use a simple tray-based tool (like one used previously on Windows) to track task switching.
</details>

---

## Actions
**TL;DR:** Immediate steps to improve workflow efficiency and security.

<!-- alt: Numbered flowchart for committed action items. -->
```mermaid
flowchart TD
    1["Select/Install Time Tracker · Max/Partner · Next Week"] --> 2["Integrate 'Inspect' into CI/CD · Max · —"]
    2 --> 3["Set up 'GitLeaks' on all local repos · Both · ASAP"]
```

<details>
<summary>Details</summary>

1.  **Tracker:** Research and select a simple time-tracking tool (preferably Open Source/Self-hosted).
2.  **CI/CD:** Move AI inspection from local hooks to the pipeline to save local time.
3.  **Discipline:** Commit to "1 hour per workday" with strict time-boxing to avoid burnout.
</details>

---

## Appendix: Options Considered
| Choice | Decision | Rationale |
| :--- | :--- | :--- |
| **Tests on Push** | Postponed | Risk of slowing down small commits |
| **Custom Tracker** | Buy/Find first | Writing a tracker is a "distraction" from the MVP |
| **Direct URL Monitor** | Adopted | Email notifications are unreliable |

