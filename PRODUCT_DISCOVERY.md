# Why TinyDevCRM? A Product Discovery Roadmap

Recently, I read through [an article by Sarah Brockett on 15 questions to ask at
the start of a new software
project](https://spin.atomicobject.com/2020/01/24/new-software-project-questions/).
I think completing this exercise for TinyDevCRM would increase clarity and focus
around the tool's value proposition, both for myself and for others.

This has increased in importance over feedback from others on
[Pioneer.app](https://pioneer.app/) and [Y Combinator Startup School Winter
2020's Week 1 Group Session](https://www.startupschool.org) that TinyDevCRM's
value proposition is not clear, and over my inability to clarify certain
selling points and answer questions on the spot at that time.

This document should be tracked at all times via version control. Since the
`git` history is open-source, no `'last_modified'` date will be provided at this
time. Ideally, this document should achieve a "done" status, and is not intended
as a living document where shared understanding is continually updated.

## Product Discovery

### 1. What problem are we trying to solve? Why?

> Custom software is not cheap. Be sure to evaluate why your customer is looking
> to dive into it in the first place. Create space for all stakeholders to
> respond, as there are likely many different perspectives behind "the why".

I'm building TinyDevCRM primarily for my own use cases. The two problems I have
coming into this project include how to keep track of my habits for the long
term, and how to keep in touch with my personal professional network.

This expounded into a broader thought process as to how I can build a platform
that could generalize these two use cases, and future-proof against any other
use cases that would crop up with respect to my personal development workflows,
and perhaps some work-related use cases as well (e.g. a client process to remind
me to rotate my personal passwords, or a work-related client to rotate my AWS
IAM keys in accordance to company policy, or a Google Home or
$SMART_DEVICE_OF_THE_DECADE habit tracker client in addition to an
always-available web-based habit tracker client).

I found some of the most powerful commonalities between these use cases as:

-   **Exposing (properly permissioned) access to the relevant underlying data**

-   **Maintaining some protocol to connect to third-party extensions /
    integrations**

-   **Triggering events to send to and notify third-party processes**

An application that can expose these three properties to a developer building a
client application, and reduce / remove the burden of having to build out a
separate backend and data store, and run resource-intensive server-side
processes, may drastically improve time-to-market or time-to-production for
arbitrary clients, and provide a central location for data management and
prevent and/or preclude separate data fragmentation and data discovery
requirements down the line.

### 2. Is there a similar product on the market? If so, how will we differentiate from it?

>   Benchmarking what other products exist is a great exercise to do during a
>   Research, Design, and Planning phase. Use this time to understand how this
>   product will be different. What value is it adding that others are lacking?

This idea came into my mind during a conversation I had with an engineer at
Stripe around their favorite internal tooling. I'm not sure how much I should
say about their response in official writing, given how I have not been granted
explicit permission to share our private conversation publicly, but I will say
that their response greatly inspired this idea. Suffice it to say I cautiously
assess this idea as possible from a first-principles perspective, and useful in
a personal and businesslike context.

With respect to my immediate problem areas and discovery into platform
consolidation, here are some products directly or tangentially related to
TinyDevCRM that I've either personally used, evaluated, or examined.

#### Habit Trackers

**[Productive](http://productiveapp.io/) (USED)**: Productive is a generic iOS
habit tracker. I've used the product now for about two years.

Pros:

-   **Native mobile support**: Productive is a native iOS application (vs.
    something like a [progressive web
    application](https://en.wikipedia.org/wiki/Progressive_web_application)),
    which means no need to install a native application to access native
    features. For example, a PWA may need to install
    [Pushover](https://pushover.net) to access native iOS notifications. A
    native application like Pushover can also make assumptions about UI/UX
    workflows, such as optimizing for touch and swipe gesture control, as
    opposed to text-based or graphical cursors or keyboard shortcuts.

    This is important to TinyDevCRM because since there's no such thing as a
    lossless translation (React Native, hybrid clouds, etc.), any connectors or
    adapters written in support of any clients to enable interop with
    third-party infrastructure should not be impeded by TinyDevCRM's
    development.

-   **Offline-first access**: I don't need to have an active Internet network
    connection in order to access or use the application, since the master copy
    of the data is colocated with the application at all times. The application
    can go wherever my iPhone goes. This is a *huge* win in comparison to a
    purely Internet-based client, since the application can be used without
    having to turn on cellular data, and can be used in environments where
    Internet may be spotty or unavailable. The client only needs to depend on
    what's available on the device, and flushes to any server asynchronously for
    backup or sync purposes.

    This implies TinyDevCRM should enable platformed clients to safely resolve
    data synchronization issues with as few issues as possible, and upon seeing
    synchronization issues, display them to the TinyDevCRM client in order to
    avoid having to dive into the actual PostgreSQL table.

-   **Forgiving UI/UX**: Unlike other habit trackers, Productive does not
    enforce default habits (you can start with however many habits makes you
    comfortable), doesn't set default habit behaviors (a habit follows a
    specific user-specified grammar and is marked on that basis, and each
    instance of that habit does not couple with other instances of that habit),
    does not limit the number of habits available (some habit tracker apps do
    this as a premium feature), and does not set hard deadlines as to when
    habits can be marked as completed or skipped.

    This forgiving nature makes it very easy to forgive yourself when you lapse
    in your habit, and empowers you to track your habits at your pace. This is
    likely the biggest reason I successfully onboarded and converted to the
    application in the first place, and conversely failed to convert to other
    platforms.

    This is important to TinyDevCRM as various client-side grammars may need to
    map to one or more relational tables, and TinyDevCRM should at least not
    impede development of these grammars.

Cons:

-   **Application crashes and data loss**: Productive is highly secure, in the
    sense that data is colocated on the physical device, and only cloud-based
    iCloud and locally based iTunes backups are allowed. This comes at the cost
    of maintaining a unitary upgrade path, with severe consequences for data
    integrity.

    One problem with upgrading iOS applications is that downloading a fresh iOS
    application results in a different workflow than upgrading an existing iOS
    application. This means that cloud-based updates to the application, which
    their developers may mean to download from scratch, may not always work with
    older, existing deploys of the application. I'm sure the developers of
    Productive do their best, but on at least two occasions, an update bricked
    my application and I lost my data. Since I don't pay for iCloud, and since
    other cloud-based backup solutions are not available, and since I don't have
    a MacBook Pro available anymore for local iTunes backups (and I forgot the
    iTunes iPhone backup password), I couldn't restore the application to its
    prior state. This meant that all of my habits were simply lost and I had to
    start from scratch, which greatly disrupted my personal critical workflows.

    I would love to have a habit tracker that supports could-native backups,
    encrypted end-to-end and encrypted at-rest.

    TinyDevCRM should enforce a property-based guarantee that no data loss or
    data integrity issues can be forced by any client.

-   **Lack of third-party developer extensibility**: Productive.app is a closed
    environment with no available developer API. This means that additional
    analytics and view representations around habits cannot be tracked beyond
    what the app developers have provided. It also means that no extensions
    ecosystem is possible.

    TinyDevCRM should make it easy for clients to support various protocols. For
    example, in addition to the traditional request / response client / server
    relationship (via HTTP / REST), it may also need to support pub / sub or
    another protocol for event generation.

-   **Opaque underlying data representation**: I have no idea how my data is
    saved. I would not be surprised if a NoSQL backing, and the lack of highly
    structured migrations causes the application upgrade process to result in
    the data loss I've been experiencing.

    TinyDevCRM should absolutely expose the relevant data to the user's owner.
    I'm concerned about what this may mean from a maintainer's perspective (if I
    have `sudoers` access on the box, how can others trust me), but having data
    encrypted at rest and decrypted with a client-side decryption key may work.

-   **UI/UX changes**: I don't remember which founder said this (maybe from
    Basecamp, or Fog Creek, or MicroConf), but they said the only way in order
    to gain new sales and maintain / increase user conversion was to release a
    new version of their product with new features. Not tinkering with sales
    channels like advertising or referrals, or increasing customer support or
    refactoring existing features and increasing performance. For a tool like a
    habit tracker where I need something reliable and steady in a changing
    world, **this is absolutely a deal breaker**. There have been at least two
    jarring UX changes I've experienced through Productive. I understand they
    have families to feed and investors to please, and the changes aren't bad,
    it's just that for me in this specific context, I don't like change.

    TinyDevCRM should aim for a "done" point, where only platformed clients
    change from time to time and updates involve security updates or external
    protocol support upgrades.

Ultimately, I found the cons to outweigh the pros. The data loss and lack of
visibility into the underlying data representation and confidence in application
upgrades prove especially egregious to my personal workflows. Considering how
much my personal workflow affects how effective I am at other parts of my life,
I consider it my top pressure point. I do think this application stands heads
and shoulders above everything else I've tried (which is why I gladly pay money
to Productive for usage of the application), but I think I've acquired enough
skills to take a shot at this problem myself. TinyDevCRM done right should play
a part in abstracting much of the underlying complexity behind building a habit
tracker.

__________

I haven't tried too many habit trackers because:

-   To get an apples to apples comparison, you need to purchase a premium
    subscription, which frequently requires a credit card or registration
    through Apple Pay. This is a huge turn-off towards exploration, because I
    have to remember to cancel the subscription and because there's no longer a
    property-based guarantee that my card won't get charged (since it's
    registered).

-   Onboarding onto a new habit tracker is risky. The longer it takes, the more
    likely it is that you'll stop using any habit tracker entirely. I'm
    extremely risk-averse in this regards.

#### To-Do Lists

**[Standard Notes](https://standardnotes.org/) (USED)**: I've used Standard
Notes for a year and some change now, and I have almost no complaints. It's
proven so effective in my personal usage, the ideal version of TinyDevCRM would
apply many of the learnings and benefits I've found through Standard Notes.

I use Standard Notes as a todo list application in the most basic, primitive way
possible. I have a note for every day, and each note has a bulleted list of
things to check off. If they're checked off, I suffix the point with "DONE" and
add a short description if relevant, and leave the note until I flush the
contents to my Google spreadsheets and archive the note. If the todo is not
finished, then I cut or copy and paste it to the note representing the next day.

Pros:

-   **Open-source and self-hostable**: I paid $150 or $3 / month for the 5 year
    plan. If Standard Notes ever goes under, the code is open sourced on GitHub,
    and I can host my own server on an AWS EC2 instance.

-   **Backups**: All of my Standard Notes clients back up in local files, and
    the server-side backups are (by user configuration) emailed to me every day.
    I have *one* file that I would need to import into a client in order to
    properly set up the data from my entire time on Standard Notes.

-   **Security-oriented**: All backups are encrypted at rest. The daily backups
    email contains a complementary HTML file that decrypts the data given a
    user-provided decryption key, and works without having network access.
    Clients require email authentication and multi-factor authentication is
    available. On top of this, a number-based or alphanumeric-based passcode can
    be set on each client to enable screen lock after a certain amount of time,
    in case somebody gained access to the laptop itself. On top of this,
    individual notes can be locked, requiring the top-level password to be
    passed in order to view contents. Individual notes can also be archived in
    order to hide them from the default view.

    I love this security model and have, at some point in time and to some
    degree, enabled all of the above. The more security available, the better.

-   **Extensible**: Standard Notes has a great extensions ecosystem, where
    plugins are curated to work with the latest clients. I use the Secure
    Spreadsheets option, TokenVault to store 2FA tokens, as well as the
    Solarized Dark theme in order to work long evenings on desktop Linux, where
    dynamic brightness adjustment is not always supported well on all devices.

Cons:

-   **Syncing could be better**: When there is a merge conflict between
    different versions of notes for Standard Notes, both copies persist, with a
    bright red "Conflicted copy" label present. I actually really like how both
    copies are persisted (vs. overwriting or dropping one of the copies), but
    there have been slight issues with multiple conflicted copies, and having to
    manually address those conflicts without the help of `git`'s `>>>>>>HEAD`
    syntax (since this tool is for non-technical stakeholders). Again, beautiful
    way of handling this situation, but if there are constraints enforced on the
    customer base, I think this feature could be even more powerful.

-   **[Todoist](https://todoist.com/) (USED)**: I never successfully onboarded
    to Todoist. I didn't like the grammar parsing, the binning of incomplete
    tasks, and the lack of access to the underlying data representation. Much of
    the same reasons I don't think I'm the right fit for Productive for my
    long-term habit tracker, I don't think I'm the right fit for Todoist for a
    todo list.

-   **[Remember the Milk](https://www.rememberthemilk.com/) (USED)**: I tried
    out the premium plan for Remember the Milk in order to replace my habit
    tracker. I quickly found out that the workflow for a habit tracker is much
    different than the workflow for a todo list, and quickly became overwhelmed
    and deplatformed.

I realized that to-do lists target a different use case than a habit tracker.
Some of the key differences include:

-   **Increased feature richness**: I really like how Productive kept the
    feature set to a bare minimum. Here are your habits for the day, here are
    your stats for each habit, and here's how you migrate habits.

    By constrast, Todoist and Remember the Milk have extremely powerful grammars
    that fit well with superusers, but at least for me, cost a learning curve
    that I didn't want to pay off the bat and didn't see myself needing (I need
    a way to update a habit's frequency, but I don't need to filter habits by a
    particular tag).

-   **Targeted towards organizations**: Both Todoist and Remember the Milk have
    enterprise plans for teams, and I'm guessing that most of their revenue
    comes from charging enterprise prices for usage of their platforms by teams
    and organizations, like most SaaS companies. If I had to guess, they are
    optimizing the single-user platform to make the transition to multi-user
    capabilities as seamless as possible, hence things like single-user feature
    richness. Productive at least doesn't have this aspiration, and optimizes
    the experience for a single user. I like this notion of targeting a single
    user and wish to keep it for TinyDevCRM.

#### CRMs

-   **[MonicaHQ](https://www.monicahq.com/) (LOOKED AT)**: This is the closest
    product I've found that represents something akin to what I want to build.
    Monica's goal is to help people build more meaningful relationships.
    Multiple people have recommended that I look into Monica HQ instead of
    building my own product, and their advice takes great credence for this
    analysis.

    Ultimately, I decided to decline proceeding with paying for Monica or
    deploying my own instance of Monica. The honest, largest answer as to why is
    because by the time people recommended it to me my heart was already set on
    building my own "CRM". However, there are a number of concerns I immediately
    had after reviewing the README:

    -   **Low prices**: At $9 / month, the pricing seems both quite high in
        comparison to Productive (where I am paying $9 / year or **75 cents /
        month** for premium) and Standard Notes (where I paid $149 / five years
        or **$2.48 / month** for premium), and yet low enough where I'm
        concerned development (including security patches) may cease sometime in
        the future. As a point of reference, Slack (the web-based chat /
        collaboration tool) charges somewhere around $200 / user for low-grade
        enterprise plans:

        > “On Business Plus, $208.70 per user (850 Users, annual plan). It's an
        > unadvertised tier between business and enterprise that gives some
        > additional features, including the eDiscovery API for compliance.”
        >
        > [Capiche.com's post on Slack
        > pricing](https://www.capiche.com/e/slack-pricing)

        That is the lowest-grade Slack plan, and I believe that they run up to
        $700 / user / month. I don't think MonicaHQ has enterprise-grade plans
        at all, which means they can't make up for the shortfall in revenue from
        single users with enterprise users. Given my extremely positive
        experiences with todo lists and habit trackers, I would pretty happily
        pay **$30-50 / month** to have a Standard Notes like experience for
        Productive, potentially with added analytics, given an explicit value
        prop, to have a guarantee of software that [lasts 50
        years](https://bytes.yingw787.com/posts/2020/01/13/50years/). I'm not
        sure how common this is though. I don't know if a company created off of
        users like me would be viable.

    -   **Application of unstructured data**: To be clear, I think one goal of a
        CRM for non-technical stakeholders is to colocate all the requisite
        information in one place. However, my optimization constraints are quite
        different from the average non-technical user's; I am worried about
        cost, performance, and security, and not convenience and ease of use.

        I'm concerned that adding blob storage would drastically complicate
        things. How would daily backups work if you were to naively send a
        permissioned snapshot of blobs to a user, and pay for that network
        traffic and machine instance time? Do you even encrypt blobs, and if so,
        how much overhead would that take up? How would storage costs increase
        with blobs? What kinds of new correctness issues and security overhead
        would take place if data was colocated in two different locations
        (database and blob storage)?

        To me, unstructured data like this isn't just unnecessary, it's a
        dangerous boundary to cross when establishing feature requirements.

    -   **Fixed data representation**: The API documentation for Monica is
        admirable, and one thing it focuses on really well is how each of the
        underlying models is backed. You can describe relationships,
        conversations, photos, and perhaps most importantly for TinyDevCRM, how
        to structure reminders. However, since I don't want the strict
        limitation of fixed database models, and since I am a technical
        stakeholder, I'd want to have something a bit more generalized, like
        `/views` or `/triggers`.

    Overall, I think Monica HQ is a great standalone CRM, very reasonably
    priced, and probably fine for 80-90% of personal tasks. But I don't think
    it's the right fit for me.

I don't think there are other CRMs that are targeted towards technical
stakeholders for personal use. The vast majority of CRM solutions are
enterprise-first, targeted towards non-technical stakeholders and users, and not
a fit at all for my needs.

#### Integrations Frameworks

There's actually quite a few integrations frameworks available, which suit the
needs of technical stakeholders and users like me far better.

-   **[Zapier](https://zapier.com/home)**: Zapier is the quintessential
    integrations company. As I'm writing this, their landing page describes
    1,500 integrations available. It's a terrific resource if you're building
    something for a company, or if you're a non-technical stakeholder.

    For my use case, I'd prefer to have a standards-compliant protocol for
    managing third-party extensions and integrations, rather than building out
    my own platform. Building a platform is a *lot* of work, and a lot of effort
    to maintain. I'd also prefer to have an API rather than a client. The
    fundamental limitation is that it's closed-source. Even as Zapier is a
    profitable company with a highly admirable funding journey, I'm not sure
    where it will be in 50 years. That's not risk I'm willing to bear.

-   **[tray.io](https://tray.io/)**: I think this is a platform similar to that
    of Zapier. Also disqualified due to closed-source nature.

-   **[IFTTT](https://ifttt.com/)**: IFTTT used to be open for third-party
    developers, now their landing page is just a "let's talk" form with a navbar
    item that says "For businesses". I'm not sure how they're doing, but this is
    the kind of risk I don't want to bear.

-   **[`huginn`](https://github.com/huginn/huginn)**: `huginn` is a "system for
    building agents that perform automated tasks for you online". This, along
    with Monica HQ, is the closest a solution has come to solving my problem.
    However, it's quite feature-rich; it has plans in order to abstract away
    agents into separate Ruby gems, but understandably it takes time to build
    alignment within the open source community and time for unpaid developers to
    do so.

    I'm definitely going to review `huginn` (e.g. using `gource`) to see whether
    I can get some inspirations on protocol design and selection.

-   **[`n8n.io`](https://n8n.io/)**: `n8n` markets itself as a "Free and
    Self-hostable Alternative to Zapier / tray.io". It seems nice.

    But it's based on `node` and the `npm` ecosystem. I'm not sure if I have to
    explain why the `node` ecosystem isn't attractive for a project that
    hopefully lasts a lifetime without too much change. I generally find `node`
    to generate extremely pretty clients, but at the cost of resource usage
    intensity and stability. For example, I use `gtop` on my IBM ThinkPad T42
    from 2003, and while I keep `gtop` running because I haven't used my T42 for
    too much yet, and because I want to know my computer is still alive, `gtop`
    alone consumes 10% of my Intel Pentium, while everything else takes up
    around 0.1% CPU (including `htop`, which I keep running in a concurrent
    window using `tmux`).

    Disqualified.

### 3. Are there other products or tools that we can, should, or need to integrate with?

> Does the product include a wearable device? Does it need to tie into other
> internal or external systems? Integrations will shape both the design and
> development approach, so knowing about them from the get-go is ideal.

I don't think there are any other products or tools to form explicit
integrations within this walled garden.

I'm pretty sure of the initial tech stack I'd like to use to get the initial 0.x
releases out for feature parity. React.js (MIT licensed, created and backed by
Facebook engineers, battle-tested and with battle-tested dependencies like
`react-router` and `redux`), Python (#1 or #2 most common programming language
in the world, aged the past 20 years quite well, learned lessons from 2 -> 3
migration), and PostgreSQL (most popular open-source SQL database in the world,
[apparently a better wire
protocol](https://www.cockroachlabs.com/blog/why-postgres/), a great extensions
ecosystem, enterprise and NewSQL enterprise support, a great procedural
extensions DSL, and twenty years of development including experience with bugs
like [fsyncgate](https://danluu.com/fsyncgate/)).

In terms of protocols: REST on HTTP seems reasonable; REST is explicitly a
stateless protocol, and hypertext is necessary to serve HTML, which I'd like to
use to create graphics-based clients vs. purely CLI or text-based clients. SQL,
or the PostgreSQL flavor of SQL, is another standard to support. Then x86_64 as
the underlying ISA. Everything else should be disposable or mutable as needed.

## Business Discovery

### 4. What value are we providing to our business?

> Will the product help with sales? Will it provide impactful data? What is the
> reason our business is seeking to create the software?

If I were to "categorize" myself as the "business", I would be creating the
software in order to maximize my personal tooling / infrastructure workflows. I
think it will go a long ways towards creating stronger relationships, keeping
track of things better, minimizing regret, and maximizing antifragile behaviors.

In addition, this tool can ideally serve as the basis for an underlying data
lake architecture for businesses, although TinyDevCRM isn't targeted towards
businesses. Account-based authentication, automated agents (bot helpers), and
other business-like features can still prove useful to personal infrastructure
optimization.

### 5. What does success look like, and how will we measure it?

> Understanding expectations is crucial. Work with your team to create a shared
> understanding of what success is and how you might measure it.

Coming up with heuristics is quite hard, so this list is most likely incomplete:

-   If I can create both a tele-rolodex and a habit tracker *without having the
    need to create any server-side processes* or stand up my own database and
    segment my data, that would be a major validator that TinyDevCRM acts as a
    useful platform. As in, I can re-use TinyDevCRM's authentication protocols,
    I can see what habits are being tracked via TinyDevCRM's dashboards, and I
    feel no need to augment TinyDevCRM for the express purposes of making my
    clients easier to develop.

-   *Time to create a barebones client is measured in days or hours or even
    minutes instead of weeks*. For example, it'd be really cool to have some
    HTML/CSS embedded widgets that can create an analytics dashboard akin to
    [Apache Graphite](https://graphiteapp.org/)'s dashboard in a few hours
    instead of weeks. Something like MailChimp's plain / advanced embedded forms
    feature, where it serves up a tiny amount of HTML / CSS, with minimal
    JavaScript, and the API doing all validation, would be very cool to do.

-   I would say if I dogfood TinyDevCRM and I don't miss having Productive, and
    I'm not at all worried about the application silently crashing and feel the
    need to check uptime status constantly, I would say TinyDevCRM has crossed
    the Rubicon in terms of usability.

I would measure success using metrics like:

-   number of dependencies involved, and how many of them are properly licensed,
    vendored, and audited to measure build complexity and maintainability
-   test coverage reports (branch coverage, PBT results, formal modeling and
    race condition analysis, etc.) to measure application correctness
-   resource utilization reports (CPU, I/O, memory / swap, # of live sockets,
    disk usage) to measure application efficiency
-   profiling / instrumentation reports, to measure application performance
-   lines of code (LOC), number of regressions introduced, etc. to measure
    application simplicity and composability

### 6. What business risks or blockers exist?

> For example, is there a crucial integration that we need to work with IT on,
> but IT is booked out for 6 months? Is there a stakeholder who has the true
> vision of the product ,but they'll be on leave at the start of the project?

There aren't realistically too many business risks or business blockers, since
this isn't designed towards becoming a business, and since this is a greenfield
project with no business-level dependencies.

I do want this to increase my BATNA (best alternative to negotiated agreement),
but I'm not sure how. I don't think increasing my technical skills will
necessarily improve my BATNA the same way as trying to monetize this tool as a
product. If I do try to monetize this, for example to recoup my AWS bills, I'm
concerned about user direction steering this product that I intend to use for
myself.

I'm thinking in this case, I would build this application the way I want to and
attempt to de-risk the product and software development aspect of building a
"business", and if significant user interest exists, then fork the project into
"BiggerDevCRM" or something, with [enterprise-grade feature
support](https://enterpriseready.io) and work on the sales / marketing aspect of
building a business.

### 7. Who are the key stakeholders, and what kind of access will we have to them?

> Use this time to map out who your stakeholders are and set expectations for
> how engaged you will need them to be. How might you ensure the project never
> gets blocked from a lack of stakeholder feedback?

I am the only stakeholder in this project for now. If I do decide to monetize
this tool, other stakeholders will be paying customers only. I don't intend on
taking on investment for this tool, and if I do intend on monetizing, I will be
choosing a very specific niche of like-minded thinkers, in order to remove
issues of alignment or shared understanding (and consequently, build a more
powerful product by making certain assumptions about the end user).

Access between me and any paying customers would mosty likey be through email.

## User Discovery

### 8. Who will use the product?

> At the end of the day, we're building the software for people. Who are those
> people? What are their goals, motivations, and frustrations?

I will be the one primarily using the product.

My goal is to optimize my life going into the future. I want a consistent and
stable tool powerful enough to platform other tools, where I have personally
audited and sourced all dependencies and confirmed this is a product that I
myself trust and dogfood on, all this in order to ensure I remain functioning at
a high level.

My frustrations around existing solutions revolve around unnecessary product
changes, data loss and data integrity issues, inability to create arbitrary
user-defined extensions, and broken code or broken features, which typically are
issues of communications, lack of alignment, or external forcing functions like
delivering returns.

### 9. What value are we providing to users?

> Why would folks be inclined to use the tool we're creating? Understanding how
> we are providing value to users will help us determine which features to
> include and how to prioritize them.

Ideally, I would provide myself with the value of supercharging my personal
tooling / infrastructure workflows, and my personal development workflows, over
the very long term with minimal additional effort after feature parity.

### 10. What risks exist if a poor-intentioned user has access to the product?

> Though we'd love to assume only well-intentioned people will use our software,
> the reality is that this may not be true. What kind of trouble could occur if
> a villainous persona gets their hands on our software? Determine what the
> risks are so we can design to mitigate them?

Significant damage could exist if a poorly intentioned user has access to this
product. After locking down and building to feature parity, the next immediate
concern would be security and permissioning.

Ideally, all data will be locked at a fina granularity (row-level or
cell-level), and a comprehensive permissioning system will exist in order to
monitor user access. This will comprise part of the journey towards feature
parity and isn't meant as an add-on feature.

### 11. Will we have access to users for research and testing?

> Having access to users is crucial for validating the overall product and
> workflows we create. Be sure to begin the process of locating and scheduling
> time with users as soon as you can.

I intend on posting occasionally to Hacker News, Lobste.rs, YC Startup School,
and Pioneer.app in order to garner feedback. Otherwise, I will see whether I can
use TinyDevCRM, and start building out my habit tracker and tele-rolodex tool to
see what might be missing from TinyDevCRM.

## Project Discovery

### 12. What key dates exist?

> Is there an event where someone hopes to demo the software? Is the software
> itself tied to a specific time of year? It's important to know if there are
> date-sensitive deliverables to ensure on-time delivery is achieved.

**February 3rd, 3AM ET**: Pioneer.app progress submission deadline.

**February 6th**: YC Startup School Winter 2020 Group Session 2

**February 8th**: depart for Chicago to see friends. I will be embarrassed if I
don't have something ready by this date (1 week's time) to show off.

**February 10th, 3AM ET**: Pioneer.app progress submission deadline.

**February 13th**: DC Hack and Tell Meetup

**February 17th, 3AM ET**: Pioneer.app progress submission deadline.

**February 20th**: YC Startup School Winter 2020 Group Session 3

**February 24th, 3AM ET**: Pioneer.app progress submission deadline.

**March 2nd, 3AM ET**: Pioneer.app progress submission deadline.

**March 5th**: YC Startup School Winter 2020 Group Session 4

**March 12th**: YC Startup School Winter 2020 Group Session 5 **(TRY TO FINISH
UP TINYDEVCRM BY THIS DATE)**

**March 16th, 8PM PT**: Deadline to apply to Y Combinator's Summer 2020 batch. I
would hope that before this date TinyDevCRM would be finished, and I could pitch
BiggerDevCRM (a B2B version of this application) as part of any YC application.

### 13. What are the expected deliverables?

> The detailed scope of the projet will shape over time, but at the start of a
> project, high-level deliverables should be determined. Is it a mobile app? Is
> it an API? Does part of the project include training and onboarding the
> customer's developers once the product is ready to hand off?

High-level deliverables:

-   A client-side dashboard that can provide visibility into user data, a way to
    easily create and integrate protocol-based events, and a way to upload,
    update, and migrate data.

-   A server-side process that can deliver what the client needs, and maintain
    the database.

-   A tool in order to package all this functionality up into an RPM / .deb
    file.

### 14. Who is the primary decision maker?

> At the end of the day, who gets to make the final decisions? This ideally
> should be one person, in order to avoid a decision paralysis or design by
> committee.

I am, period.

### 15. How might we best work together?

> What are the communication preferences across the team? Will you be working
> on-site together, or remotely? How often and to what capacity can and will
> your stakeholders be involved? Set the cadence for the project. I recommend
> setting up recurring calendar events early on, well before calendars get full
> and you become blocked on stakeholder feedback.

I'm currently working on this problem by myself, and since the product is for
myself, there's no feedback loops in order to release / ship product. The flip
side is this project may become rather lonely without external support and if I
don't ship at a fast enough speed. In order to combat this, I can do the
following:

- Publish a weekly stand-up thread, either on my personal techblog [Bytes by
  Ying](https://bytes.yingw787.com), or on a dedicated notes.tinydevcrm.com
  release website. The former might be easier to get started with, and the
  latter would be if there was signficant inertia in the project and a clear
  ability to monetize somehow (which again isn't a top priority for me).

- Continue to publish benchmarks on both [Pioneer.app](https://pioneer.app) and
  on [the Indie Hackers Daily Stand-up
  thread](https://www.indiehackers.com/group/daily-stand-up), and possibly ask
  folks for support or where they get sources of support there.

- Have a public issues dashboard or Kanban board where users would be able to
  (anonymously?) suggest feedback. Since I'm using GitHub at the moment, keeping
  track of issues on GitHub Issues would be a start. I can link to GitHub Issues
  from various points on TinyDevCRM, and ask around for the best automation /
  tooling / infrastructure FOSS maintainers use on GitHub Issues.

I'll likely have additional ways in order to establish feedback loops, but
having as much feedback and constructive criticism as frequently as possible is
critical to my professional development and to the success of this project.
After ensuring the proof of concept works and beginning the software development
lifecycle, establishing and exercising means of communications will be amongst
my highest priorities (#1 - #3).
