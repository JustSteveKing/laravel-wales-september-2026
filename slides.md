---
theme: ./theme
title: Waiting is a Feature
info: |
  ## Waiting is a Feature
  Modelling processes that stop, in Laravel.

  Laravel Wales, September 2026 — Steve McDougall
layout: cover
drawings:
  persist: false
transition: slide-left
duration: 35min
---

###### Laravel Wales · September 2026

# Waiting is a Feature

Modelling processes that stop, in Laravel

<v-click>

<div class="mt-12 text-sm" style="color: var(--jsk-muted)">
  Steve McDougall - juststeveking.com - @juststeveking
</div>

</v-click>

<!--
Thirty-five minutes. Don't rush the opening — slides three to seven are the whole
argument and everything after is evidence.

Say the title, take forty-five seconds on the intro, then straight into order 4471.
-->

---

###### Who's talking

# Steve McDougall

<v-click>

Developer Educator and API consultant. Based in Wales.

</v-click>


<div class="grid grid-cols-2 gap-12 mt-9">

<div v-click>
  <div class="kicker mb-3">Background</div>
  <div class="space-y-1.5 text-sm">
    <div>15+ years building software</div>
    <div>PHP-FIG core committee</div>
    <div>30+ talks and workshops, internationally</div>
    <div>50,000+ developers reached through writing and teaching</div>
  </div>
</div>

<div v-click>
  <div class="kicker mb-3">Why me, for this talk</div>
  <div class="text-sm leading-relaxed">
    I help SaaS teams fix slow, unreliable APIs without rewriting everything.
    <div class="mt-3">
      Which means I spend my working life inside other people's integrations - and the thing I find broken, over and over, is never the code that runs.
    </div>
    <div class="mt-3" style="color: var(--jsk-heading)">
      It's the code that was supposed to wait.
    </div>
  </div>
</div>

</div>

<!--
Forty-five seconds. Genuinely. The room did not come for my CV, and the next slide
is the one that has to land.

The only two lines that matter here:

  - I fix APIs for a living, so I see a lot of codebases — this isn't one bad
    experience generalised into a talk, it's a pattern across dozens of teams.
  - And the punchline sets up the thesis: it's never the running code that's broken.

If it's a warm room, the Wales line is worth a beat — home fixture.

Skip the Royal Tank Regiment line unless someone asks afterwards. It's on the slide
because people find it interesting, not because it needs saying out loud.
-->

---
layout: center
class: px-16
---

# This is order #4471

<div class="mt-10">
  <Timeline height="5rem" />
</div>

<div class="mt-8 text-sm opacity-60 text-center" v-click>
  Placed Tuesday 09:13. Delivered Thursday 15:34. Drawn to scale.
</div>

<!--
Don't explain it yet. Let it sit for a beat.

Then: "somewhere in there is all of your code."

The three or four hairlines on the left ARE the running segments. Point at them.
Nobody in the room can see them, which is the point.
-->

---
layout: center
class: px-16
---

# Somewhere in there is all of your code

<div class="mt-10">
  <Timeline height="5rem" />
</div>

<div class="grid grid-cols-3 gap-8 mt-14">
  <BigStat value="54h 21m" label="Wall clock" sub="Order placed to follow-up email sent" tone="sleep" v-click/>
  <BigStat value="0.89s" label="Your code executing" sub="Every line, every query, every API call" tone="run" v-click/>
  <BigStat value="1 : 220,000" label="Ratio" sub="Running to waiting" v-click/>
</div>

<!--
0.89 seconds of work. 54 hours of process.

If you scaled this order down to a single day, every line of code you have ever
written for it would execute in four tenths of one second. The other twenty-three
hours, fifty-nine minutes and fifty-nine and a half seconds, this order is doing
absolutely nothing.

And "doing absolutely nothing" is what your customer calls "my order".
-->

---
layout: statement
class: text-center
---

# Scale it to one day

<div class="text-2xl mt-8 opacity-80 leading-relaxed" v-click>
  Your code runs for <span class="font-bold" style="color: var(--wf-run)">0.4 seconds</span>.
</div>

<div class="text-2xl mt-2 opacity-80 leading-relaxed" v-click>
  The process waits for the other <span class="font-bold">23 hours, 59 minutes and 59.6 seconds</span>.
</div>

<div class="rule mt-12" v-click />

<div class="text-lg opacity-60" v-click>
  We architect for the 0.4 seconds.
</div>

<!--
This is the beat to pause on.

Fifteen years of framework evolution, queue systems, horizontal scaling, octane,
optimising the container — all of it aimed at the 0.4 seconds.

The 23 hours and 59 minutes gets a varchar column.
-->

---
layout: center
class: px-14
---

# What the 54 hours is actually made of

<div class="mt-8">
  <Timeline scale="log" labels height="4rem" />
</div>

<div class="mt-10 grid grid-cols-4 gap-4 text-xs" v-click>
  <div><span class="tag wf-run">run</span> <span class="dim">your code</span></div>
  <div><span class="tag wf-signal">signal</span> <span class="dim">another system</span></div>
  <div><span class="tag wf-human">human</span> <span class="dim">a person</span></div>
  <div><span class="tag wf-sleep">sleep</span> <span class="dim">the clock</span></div>
</div>

<div class="mt-6 text-xs opacity-40 text-center">
  Log scale. The real one was unreadable, which was the point of the last slide.
</div>

<!--
Be honest about the log scale. Say it out loud — "I've stretched this so we can
talk about it, the real one is the slide before."

Walk the segments left to right:
  reserve stock, authorise payment — real work, milliseconds
  then we stop, and wait for Stripe
  risk gate — real work, 40 milliseconds
  then we stop, and wait six hours for a human
  decide and pack — real work
  then we stop, and wait two days for a van
  then one email

Three stops. That is the process. The work is the punctuation.
-->

---
layout: statement
class: text-center
---

# Every framework you have ever used<br>is about making things **run**

<div class="mt-10 text-xl opacity-70 leading-relaxed">
  Almost nothing is about making things <span class="font-bold">stop</span><br>
  and then <span class="font-bold">start again correctly</span>.
</div>

<!--
THIS IS THE THESIS. Say it slowly. Everything from here is evidence.

Routing, containers, queues, Octane, caching, eager loading — verbs. Run faster,
run more, run in parallel.

Nothing in the box tells you how to stop for six hours and pick up exactly where
you left off, after a deploy, with a webhook that might arrive twice.
-->

---
layout: statement
class: text-center
---

<div class="text-3xl leading-relaxed">
  We model the running bits carefully<br>
  and the waiting bits with a
  <span class="font-mono px-2 py-1 rounded" style="background: rgba(245,158,11,0.15); color: var(--wf-human)">status</span>
  column and hope.
</div>

<!--
Land it. Then move.

The rest of the talk: first I'll prove the hope part, then I'll define waiting
properly, then I'll show you what I built when I got tired of the hoping.
-->

---
layout: section
---

###### Part one

# The code we already wrote

<!--
About seven minutes in. This section is the audience recognising their own codebase.
Get them nodding. Do not be smug about it — I wrote all of this too.
-->

---

# Exhibit A

The nine-case enum

````md magic-move
```php {all|3-11|13-17|17}
<?php

enum OrderStatus: string
{
    case Pending         = 'pending';
    case AwaitingPayment = 'awaiting_payment';
    case Paid            = 'paid';
    case AwaitingReview  = 'awaiting_review';
    case Packed          = 'packed';
    case Shipped         = 'shipped';
    case Delivered       = 'delivered';
    case Cancelled       = 'cancelled';
    case Refunded        = 'refunded';

    // pending -> awaiting_payment -> paid -> awaiting_review? -> packed -> shipped -> delivered
    // anything before packed -> cancelled
    // paid | packed | shipped -> refunded
}
```
````

<div v-click class="mt-4 text-lg" style="color: var(--wf-human)">
  That comment is the only place those rules exist.
</div>

<!--
[click] Nine cases. Every one of us has written this.

[click] And then the comment. The comment is always there.

[click] Nothing enforces it. Nothing tests it. It is a specification written in
the one part of the file the compiler ignores.

Notice something else: four of these nine cases aren't states, they're *waits*.
Awaiting payment. Awaiting review. Shipped — which really means "waiting for a van".
Paid — which often means "waiting for the warehouse to open".
-->

---

# Exhibit B

Where the process actually lives

<div class="grid grid-cols-2 gap-x-10 gap-y-3 mt-8 text-sm">
  <div v-click><span class="tag">app/Http/Controllers/CheckoutController.php</span><div class="opacity-50 mt-1">sets <code>awaiting_payment</code></div></div>
  <div v-click><span class="tag">app/Http/Controllers/StripeWebhookController.php</span><div class="opacity-50 mt-1">sets <code>paid</code>, decides what happens next</div></div>
  <div v-click><span class="tag">app/Listeners/RouteOrderForReview.php</span><div class="opacity-50 mt-1">sets <code>awaiting_review</code> if risk is high</div></div>
  <div v-click><span class="tag">app/Jobs/PackOrderJob.php</span><div class="opacity-50 mt-1">sets <code>packed</code>, dispatches shipping</div></div>
  <div v-click><span class="tag">app/Console/Commands/SendDeliveryFollowUps.php</span><div class="opacity-50 mt-1">reads <code>shipped_at</code>, sends the email</div></div>
  <div v-click><span class="tag">database/migrations/…_add_timestamps_to_orders.php</span><div class="opacity-50 mt-1"><code>paid_at</code>, <code>reviewed_at</code>, <code>shipped_at</code>, <code>follow_up_sent_at</code></div></div>
</div>

<div v-click class="mt-8 text-lg">
  Six files. Zero of them contain the process.
</div>

<!--
Click through them one at a time. Let people count.

The killer question at the end: if a new developer joins on Monday and asks
"what happens to an order after payment?" — which file do you open?

Answer: all six, in the right order, holding the sequence in your head.

That is what we mean when we say a codebase is hard to onboard into. It isn't
the volume of code. It's that the important thing isn't written down anywhere.
-->

---

# Exhibit C

The scheduled command that is secretly a `sleep`

````md magic-move
```php {all|4-7|9-12}
// app/Console/Commands/SendDeliveryFollowUps.php

Order::query()
    ->where('status', OrderStatus::Shipped)
    ->whereNull('follow_up_sent_at')
    ->where('shipped_at', '<=', now()->subDays(2))
    ->each(function (Order $order): void {
        Mail::to($order->customer)->send(new DeliveryFollowUp($order));

        $order->update([
            'follow_up_sent_at' => now(),
        ]);
    });
```
````

<div v-click class="mt-5 text-lg">
  This is <code>sleep(48 hours)</code>, implemented as a cron job, a status check
  and a nullable timestamp.
</div>

<!--
[click] Look at that where clause. Status equals shipped, follow up not sent,
shipped more than two days ago.

Three conditions. Together they mean one thing: "has this order finished waiting?"

[click] And the nullable timestamp at the bottom is the only thing stopping us
emailing the customer every minute for the rest of eternity.

We wrote a distributed timer using a cron job and a null check. And we all
thought that was normal, because there was nothing else in the box.
-->

---

# Exhibit D

The webhook that has to guess

````md magic-move
```php {all|5|7|9-17|18-21}
public function __invoke(Request $request): Response
{
    $event = $this->verifyStripeSignature($request);

    $order = Order::where('payment_intent', $event->intentId)->firstOrFail();

    if ($order->status === OrderStatus::AwaitingPayment) {
        $order->update(['status' => OrderStatus::Paid, 'paid_at' => now()]);

        if ($order->risk_score >= 70) {
            $order->update(['status' => OrderStatus::AwaitingReview]);

            ReviewQueue::push($order);
        } else {
            PackOrderJob::dispatch($order);
        }
    }
    // What if the order was already paid?
    // What if it was cancelled thirty seconds ago? What if Stripe sends this webhook twice?

    return response()->noContent();
}
```
````

<!--
[click] We look the order up by payment intent, because we have to reverse-engineer
which process this webhook belongs to.

[click] Then this line. This is the tell. `if status === awaiting_payment`.

We are asking the database "where had we got to?" — because we genuinely do not
know. The process has no memory. The status column is a save file for a state
machine nobody wrote.

[click] And then the business logic. The routing decision for the entire order —
review or pack — lives inside an HTTP controller for a third party's webhook.

[click] And these three comments. Every one of them is a real bug that has
happened to every one of us.

Stripe does send it twice. That's not a bug in Stripe, that's at-least-once
delivery, and it's the correct design. We're just not ready for it.
-->

---
layout: center
class: px-20
---

# Five ways this fails

<div class="mt-8 space-y-4">
  <div v-click><span class="font-bold" style="color: var(--wf-signal)">The race.</span> <span class="opacity-70">The webhook arrives before the row that says we're waiting for it.</span></div>
  <div v-click><span class="font-bold" style="color: var(--wf-sleep)">The lost sleeper.</span> <span class="opacity-70">The scheduler didn't run for four hours. Nothing anywhere knows a follow-up is late.</span></div>
  <div v-click><span class="font-bold" style="color: var(--wf-human)">The impossible state.</span> <span class="opacity-70">Two writers, two transitions, one column. Now it's <code>packed</code> and refunded.</span></div>
  <div v-click><span class="font-bold" style="color: var(--wf-run)">The unanswerable question.</span> <span class="opacity-70">"What is order 4471 waiting for?" — nobody can answer without reading code.</span></div>
  <div v-click><span class="font-bold">The half-done process.</span> <span class="opacity-70">Charged, stock reserved, then rejected at review. Now what?</span></div>
</div>

<!--
Go through these at pace, one click each. Don't over-explain — the audience will
have lived at least three of them.

Linger on the fourth. "What is order 4471 waiting for" is the question a support
agent asks you on Slack at 4pm on a Friday, and the honest answer is "give me
twenty minutes and I'll read the webhook controller".

And the fifth is the one that costs actual money. Half-done processes are how
you end up refunding people manually from a spreadsheet.
-->

---
layout: statement
class: text-center
---

<div class="text-3xl leading-relaxed">
  None of these are bugs in the running code.
</div>

<div class="text-3xl mt-6 leading-relaxed opacity-80">
  Every single one is a bug in the <span class="font-bold" style="color: var(--wf-human)">waiting</span>.
</div>

<!--
This is the hinge of the talk. Roughly fifteen minutes in.

Nobody's `reserve stock` method is wrong. Nobody's Stripe integration is wrong.
The code that runs is fine. It's well tested. It has type hints.

Everything that broke, broke in the gaps between it.
-->

---
layout: section
---

###### Part two

# Four kinds of waiting

<!--
Short section — three slides — but it's the bit that makes this a talk about
architecture rather than a package demo. Don't skip it for time.
-->

---
layout: center
class: px-12
---

# Not all waiting is the same

<div class="mt-6 text-sm">

| | Waiting on | Bounded? | Arrives as | If it never comes |
|---|---|---|---|---|
| **1** | <span style="color: var(--wf-human)">a person</span> | no | a click, eventually, maybe | it rots silently |
| **2** | <span style="color: var(--wf-signal)">another system</span> | sort of | a webhook you don't control | you're permanently out of sync |
| **3** | <span style="color: var(--wf-sleep)">the clock</span> | yes | *nothing arrives at all* | it simply never happens |
| **4** | <span style="color: var(--wf-run)">yourself</span> | yes | a retry | you gave up without telling anyone |

</div>

<div v-click class="mt-10 text-lg text-center">
  Laravel ships a first-class answer for exactly one of these.
</div>

<!--
Walk the table. The interesting column is the third one, "arrives as".

Kind three is the strange one — nothing arrives. There is no event. The only
thing that changes is that time passes. Which means something has to be actively
looking, forever, or it never happens.

[click] And then: the queue. Retries, backoff, max attempts, failed_jobs table.
Genuinely excellent. Row four is solved.

We have been pretending row four's solution covers rows one to three for about
a decade.
-->

---
layout: center
class: px-20
---

# The disguise

```php
ProcessDeliveryFollowUp::dispatch($order)
    ->delay(now()->addDays(2));
```

<div class="mt-8 space-y-3 text-base">
  <div v-click class="opacity-80">That is a business process that pauses for two days.</div>
  <div v-click class="opacity-80">It has no name, no status, and no row you can query.</div>
  <div v-click class="opacity-80">You cannot cancel it. You cannot ask it what it's waiting for.</div>
  <div v-click class="opacity-80">If Redis is flushed on Thursday, it silently never happens.</div>
</div>

<div v-click class="mt-10 text-xl font-bold" style="color: var(--wf-sleep)">
  A delayed job is a business process wearing a disguise.
</div>

<!--
This slide usually gets the biggest reaction, because everyone has done it and
it feels so reasonable when you write it.

[clicks] Take them one at a time.

The Redis one is the killer. A delayed job lives in your queue backend. Your
queue backend is, correctly, treated as ephemeral infrastructure. You have
almost certainly flushed it during an incident. And when you did, you deleted
part of a customer's order process and nothing anywhere recorded that.

[click] Land the line.
-->

---
layout: section
---

###### Part three

# If waiting were first-class

<!--
Roughly nineteen minutes in. Now we build the spec BEFORE showing the package,
so the package feels like a consequence rather than a sales pitch.
-->

---
layout: center
class: px-16
---

# What would have to be true

<div class="grid grid-cols-2 gap-x-10 gap-y-5 mt-8 text-sm">
  <div v-click><span class="font-bold">Durable.</span> <span class="opacity-65">Survives deploys, restarts, and a flushed Redis.</span></div>
  <div v-click><span class="font-bold">Named.</span> <span class="opacity-65">"awaiting <code>payment_captured</code>", not "status = 4".</span></div>
  <div v-click><span class="font-bold">Addressable.</span> <span class="opacity-65">A webhook must be able to find the thing that's waiting.</span></div>
  <div v-click><span class="font-bold">Inspectable.</span> <span class="opacity-65">One command, one answer, no source code.</span></div>
  <div v-click><span class="font-bold">Bounded.</span> <span class="opacity-65">Everything that waits must be able to give up.</span></div>
  <div v-click><span class="font-bold">Reversible.</span> <span class="opacity-65">If we stop for good, unwind what already happened.</span></div>
  <div v-click><span class="font-bold">Race-proof.</span> <span class="opacity-65">The signal may arrive before we're ready to receive it.</span></div>
  <div v-click><span class="font-bold">Idempotent.</span> <span class="opacity-65">The queue will deliver twice. The side effect happens once.</span></div>
</div>

<div v-click class="mt-8 text-center text-lg">
  That's a specification. So I wrote the thing that implements it.
</div>

<!--
Eight bullets, click through them. This is the payoff of parts one and two —
every bullet maps back to a failure mode we just looked at.

Durable → the lost sleeper.
Named → the unanswerable question.
Race-proof → the race.
Reversible → the half-done process.
Idempotent → Stripe sending it twice.

[click] Then the reveal. Keep it low-key. "So I wrote the thing" — not
"INTRODUCING".
-->

---
layout: center
class: text-center
---

# `juststeveking/workflow-engine`

<div class="mt-6 text-lg opacity-70">
  A signal-driven, step-based workflow engine for processes<br>that unfold over hours or days.
</div>

```bash
composer require juststeveking/workflow-engine
php artisan migrate
```

<div class="mt-6 text-sm opacity-55">
  Two tables: <code>workflow_instances</code>, <code>workflow_signals</code>.<br>
  One requirement: a queue worker that stays running.
</div>

<!--
Thirty seconds on this slide, no more. Nobody came to watch a composer require.

The one thing worth saying: the durability lives in your database, not in Redis.
That's the entire difference between this and a delayed job.
-->

---

# A workflow is a list of steps

````md magic-move
```php {all|7|12-21}
<?php

final class FulfilOrderWorkflow implements WorkflowDefinitionContract
{
    public static function name(): string
    {
        return 'fulfil_order';
    }

    public function steps(): array
    {
        return [
            ReserveStockStep::class,
            ChargeCustomerStep::class,
            RiskGateStep::class,
            ManualReviewStep::class,
            ReviewDecisionStep::class,
            PackOrderStep::class,
            ShipOrderStep::class,
            DeliveryFollowUpStep::class,
        ];
    }
}
```
````

<!--
[click] A stable name. This is what you'll type in the console and store in the
database, so it outlives class renames.

[click] And the steps. Read them out loud.

Now go back to Exhibit B — the six files. This is the same process. The
difference is that you can read it in one breath, from one file, and there's
nowhere else it could be hiding.

If a new developer asks "what happens after payment?", you point here.
-->

---

# A step is a class

````md magic-move
```php {all|5-7|9|14-22}
<?php

final class ReserveStockStep implements WorkflowStepContract
{
    public function __construct(
        private readonly Inventory $inventory,
    ) {}

    public function execute(WorkflowContext $context): StepResult
    {
        // Process goes here
    }

    public function timeoutSeconds(): ?int
    {
        return null;
    }

    public function maxAttempts(): int
    {
        return 1;
    }
}
```
````

<!--
[click] Constructor injection. It comes out of the container like everything else
in your app. There is no special base class, no magic, no static registry of
handlers.

[click] Execute takes an immutable context and returns a result. That's the whole
contract. Note the context is immutable — you can't mutate your way into a state
where the database and memory disagree. Data moves forward only, through the
return value.

[click] And two knobs: how long this step is allowed to take, and how many
attempts it gets.
-->

---
layout: center
class: px-14
---

# Five things a step can return

<div class="text-sm dense">

````md magic-move
```php
// Done. Merge this into the context and move to the next step.
StepResult::complete(['stock_reserved' => true]);
```
```php
// Done. Merge this into the context and move to the next step.
StepResult::complete(['stock_reserved' => true]);
// Stop. Park until someone sends this named signal.
StepResult::await('payment_captured');
```
```php
// Done. Merge this into the context and move to the next step.
StepResult::complete(['stock_reserved' => true]);
// Stop. Park until someone sends this named signal.
StepResult::await('payment_captured');
// Skip. Jump straight to a specific step.
StepResult::goto(PackOrderStep::class);
```
```php
// Done. Merge this into the context and move to the next step.
StepResult::complete(['stock_reserved' => true]);
// Stop. Park until someone sends this named signal.
StepResult::await('payment_captured');
// Skip. Jump straight to a specific step.
StepResult::goto(PackOrderStep::class);
// Stop. Wake up in 48 hours and carry on.
StepResult::sleep(60 * 60 * 24 * 2);
```
```php
// Done. Merge this into the context and move to the next step.
StepResult::complete(['stock_reserved' => true]);
// Stop. Park until someone sends this named signal.
StepResult::await('payment_captured');
// Skip. Jump straight to a specific step.
StepResult::goto(PackOrderStep::class);
// Stop. Wake up in 48 hours and carry on.
StepResult::sleep(60 * 60 * 24 * 2);
// Stop for good. Unwind everything already done.
StepResult::fail('Rejected during manual review');
```
````

</div>

<div v-click class="mt-8 text-lg text-center">
  Three of the five are ways to <span class="font-bold" style="color: var(--wf-human)">stop</span>.
</div>

<!--
This is the centre of the talk. Slow down here.

This is a vocabulary for stopping. That's the thing we never had.

Before today, "stop and wait for Stripe" and "stop and wait two days" and "stop
because a human said no" were all the same thing: an UPDATE to a varchar column,
plus some code somewhere else that hopefully agrees about what that varchar means.

[click] Three of the five are stops. In a normal framework, zero of the five are.

Also worth pointing out: `goto` looks alarming and isn't. It's a branch, and it's
explicit and traceable, which is more than can be said for `if ($order->risk_score
>= 70)` buried in a webhook controller.
-->

---

# Waiting on a system

````md magic-move
```php {all|6-10|11-13|12|21-24}
final class ChargeCustomerStep implements WorkflowStepContract, CompensatingStep, HasRetryBackoff
{
    // constructor here
    public function execute(WorkflowContext $context): StepResult
    {
        $intent = $this->gateway->authorise(
            amountInCents: $context->get('total_in_cents'),
            customerId: $context->get('customer_id'),
            idempotencyKey: "order-{$context->get('order_id')}-charge",
        );
        return StepResult::await('payment_captured', [
            'payment_intent_id' => $intent->id,
        ]);
    }

    public function compensate(WorkflowContext $context): void
    {
        $this->gateway->refund($context->get('payment_intent_id'));
    }

    public function retryBackoff(int $attempt): int
    {
        return 10 * $attempt;
    }

    public function timeoutSeconds(): ?int
    {
        return 60 * 15;
    }
}
```
````

<!--
[click] We authorise the payment. Note the idempotency key — we'll come back to
why that's on me and not on the engine.

[click] And then we stop. `await('payment_captured')`.

The instance is now a row in the database with status `awaiting` and a signal
name. No worker is held open. No connection is held open. Nothing is in memory.
You could redeploy the entire application right now and it would not matter.

[click] The data we return alongside the await gets merged into the context, so
the compensation later knows which intent to refund.

[click] And this is the bounded bit. Fifteen minutes. If Stripe never calls us
back, this doesn't hang forever — it times out, and a timeout is an event you
can act on.
-->

---

# Branching

````md magic-move
```php {all|5|7-9|11}
final class RiskGateStep implements WorkflowStepContract
{
    public function execute(WorkflowContext $context): StepResult
    {
        $score = $context->get('risk_score', 0);

        if ($score < 70) {
            return StepResult::goto(PackOrderStep::class);
        }

        return StepResult::complete(['requires_review' => true]);
    }
}
```
````

<div v-click class="mt-8 text-base opacity-75">
  <code>risk_score</code> didn't come from our database. It arrived on the webhook,
  four seconds ago, and merged itself into the context.
</div>

<!--
[click] Read the risk score off the context.

[click] Low risk — skip the next two steps entirely and go straight to packing.

[click] High risk — carry on to manual review.

[click] And here's the nice bit. Where did `risk_score` come from? Stripe sent it.
Signal data merges into the context, so a value that originated outside your
system four seconds ago is just... there, as a normal argument to a normal
branch, in a normal class you can unit test.

Compare that to Exhibit D, where the same decision lived inside an HTTP
controller and read from a column that a different file had written.
-->

---

# Waiting on a person

````md magic-move
```php {all|5|7|10-13}
final class ManualReviewStep implements WorkflowStepContract
{
    public function execute(WorkflowContext $context): StepResult
    {
        ReviewQueue::push($context->get('order_id'));

        return StepResult::await('review_decision');
    }

    public function timeoutSeconds(): ?int
    {
        return 60 * 60 * 24;
    }
}
```
````

<div v-click class="mt-8 text-base opacity-75">
  A human is just a very slow, very unreliable webhook.
</div>

<!--
[click] Put it in front of a person.

[click] Then stop. Indefinitely. This is the six-hour segment on the timeline,
and the code for it is one line.

[click] Bounded, though. Twenty-four hours. If nobody looks at this order in a
day, something has to happen — escalate it, auto-approve it under a threshold,
email a manager. The point is that "nobody did anything" becomes an *event*
rather than an absence.

That's the thing a status column can never do. `awaiting_review` in a varchar
has no opinion about how long it's been there.

[click] Say it with a straight face.
-->

---

# Failing on purpose

````md magic-move
```php {all|5-7|9}
final class ReviewDecisionStep implements WorkflowStepContract
{
    public function execute(WorkflowContext $context): StepResult
    {
        if ($context->get('approved') === true) {
            return StepResult::complete();
        }

        return StepResult::fail('Rejected during manual review');
    }
}
```
````

<div v-click class="mt-8">

Two steps back we charged a card. One step before that we reserved stock.

<div class="mt-3 text-lg font-bold" style="color: var(--wf-signal)">Both of those now have to be undone.</div>

</div>

<!--
[click] Approved, carry on.

[click] Rejected — fail the instance with a reason. Not an exception. A decision.
This is a business outcome, not an error, and it reads like one.

[click] But now we have the fifth failure mode from earlier — the half-done
process. We've taken money and we've reserved stock for an order that is never
going to ship.

In the old world this is where someone opens a spreadsheet.
-->

---
layout: center
class: px-16
---

# Compensation runs backwards

<div class="mt-10 space-y-3 font-mono text-sm">

<div class="flex items-center gap-3 opacity-40"><span class="w-6">1</span><span>ReserveStockStep</span><span class="ml-auto">done</span></div>
<div class="flex items-center gap-3 opacity-40"><span class="w-6">2</span><span>ChargeCustomerStep</span><span class="ml-auto">done</span></div>
<div class="flex items-center gap-3 opacity-40"><span class="w-6">3</span><span>RiskGateStep</span><span class="ml-auto">done</span></div>
<div class="flex items-center gap-3 opacity-40"><span class="w-6">4</span><span>ManualReviewStep</span><span class="ml-auto">done</span></div>
<div class="flex items-center gap-3" style="color: var(--wf-human)"><span class="w-6">5</span><span>ReviewDecisionStep</span><span class="ml-auto">failed</span></div>

<div class="rule" />

<div v-click class="flex items-center gap-3" style="color: var(--wf-signal)"><span class="w-6">↑</span><span>ChargeCustomerStep::compensate()</span><span class="ml-auto">refund</span></div>
<div v-click class="flex items-center gap-3" style="color: var(--wf-signal)"><span class="w-6">↑</span><span>ReserveStockStep::compensate()</span><span class="ml-auto">release stock</span></div>

</div>

<div v-click class="mt-8 text-base opacity-70 text-center">
  Reverse completion order. Only the steps that implement <code>CompensatingStep</code>. Idempotent, because it may be retried.
</div>

<!--
[click] [click] The engine walks back up the completed steps and calls compensate
on any that know how to undo themselves.

Reverse order matters. Refund before releasing stock — because if the refund
fails you want to still be holding the stock while a human works out what
happened.

[click] Not every step needs compensation. `RiskGateStep` didn't touch anything
outside the process, so it has nothing to undo.

This is the saga pattern, and it's been in distributed systems literature for
thirty-five years. It's just never been within reach of a normal Laravel app
before, because you needed the process to be a first-class object first.
-->

---

# Sleeping properly

````md magic-move
```php {all|8-11}
final class ShipOrderStep implements WorkflowStepContract
{
    public function execute(WorkflowContext $context): StepResult
    {
        $consignment = $this->courier->book(
            orderId: $context->get('order_id'),
        );

        return StepResult::sleep(60 * 60 * 24 * 2, [
            'consignment_id' => $consignment->id,
            'shipped_at' => now()->toIso8601String(),
        ]);
    }
}
```
````

<div v-click class="mt-6 text-base">

Compare Exhibit C — a cron job, a status check, a nullable timestamp, and an
`each()` loop over the whole orders table every minute.

</div>

<div v-click class="mt-4 text-sm opacity-60">

The safety net is still a scheduled command, but it's the engine's, not yours:
<code>Schedule::command('workflow:tick')->everyMinute();</code>

</div>

<!--
[click] Two days. It's a row in the database with a wake-up time, not a job
sitting in Redis.

[click] Put this next to Exhibit C and let people sit with the difference. Same
behaviour. One of them is a sentence, the other is a file, a column, a migration
and a cron entry.

[click] Be honest — something still has to tick. The difference is it's one
generic command that knows about every sleeping process in the system, rather
than one bespoke command per process, each with its own subtly different where
clause.
-->

---

# Starting one

````md magic-move
```php {all|3-4|5-11}
$instance = $this->workflowEngine->start(
    workflowName: 'fulfil_order',
    aggregateId: (string) $order->id,
    aggregateType: 'order',
    initialContext: [
        'order_id' => $order->id,
        'customer_id' => $order->customer_id,
        'lines' => $order->lines->toArray(),
        'total_in_cents' => $order->total_in_cents,
    ],
);
```
````

<div v-click class="mt-8 text-base opacity-75">
  The aggregate pair is how the outside world finds this process later.
  Your <code>orders</code> table never needs to know a workflow exists.
</div>

<!--
[click] The aggregate pair — type and id. This is the address.

[click] And this is a design decision worth calling out: the coupling points one
way. Orders don't have a `workflow_instance_id` column. The workflow knows about
the order; the order knows nothing about the workflow.

Which means you can add this to an existing system, for one process, without
touching your domain models at all. That matters — nobody is rewriting their
checkout on a Tuesday.
-->

---
layout: two-cols
layoutClass: gap-8
---

# The webhook, before

<div class="text-xs dense">

```php
$order = Order::where('payment_intent', $id)
    ->firstOrFail();

if ($order->status === Status::AwaitingPayment) {
    $order->update([
        'status' => Status::Paid,
        'paid_at' => now(),
    ]);

    if ($order->risk_score >= 70) {
        $order->update([
            'status' => Status::AwaitingReview,
        ]);
        ReviewQueue::push($order);
    } else {
        PackOrderJob::dispatch($order);
    }
}
```

</div>

<div class="mt-4 text-xs opacity-60">
  Knows the whole process. Decides what happens next. Breaks if delivered twice.
</div>

::right::

# and after

<div class="text-xs dense">

```php
$awaiting = $this->workflows->findAwaitingSignal(
    signal: 'payment_captured',
    aggregateId: $event->metadata['order_id'],
    aggregateType: 'order',
);

foreach ($awaiting as $instance) {
    $this->engine->signal(
        instanceId: $instance->id,
        signal: 'payment_captured',
        signalData: [
            'payment_id' => $event->paymentId,
            'risk_score' => $event->riskScore,
        ],
        deliveredBy: 'stripe_webhook',
    );
}
```

</div>

<div class="mt-4 text-xs opacity-60">
  Knows nothing about orders. Reports an event. That's the whole job.
</div>

<!--
Give them a moment to read both.

The line count is almost identical. That's deliberate — I'm not claiming this
makes your code shorter. It doesn't.

What changed is *what the controller knows*. On the left it knows the entire
fulfilment process. On the right it knows one fact: Stripe says payment was
captured for order 4471.

If the process changes next quarter — new fraud step, new approval tier — the
left-hand file has to change. The right-hand file never changes again.

And `deliveredBy` gives you an audit trail for free. Six months from now,
"who moved this order forward?" has an answer.
-->

---
layout: center
class: px-16
---

# "What is order 4471 waiting for?"

<div class="terminal mt-6 text-xs leading-relaxed p-5">

```txt
$ php artisan workflow:show 4471

  Workflow     fulfil_order
  Instance     01JD9K2M7QX4ZR8V3NPYW6C5TB
  Aggregate    order:4471
  Status       awaiting
  Step         4 of 8   ManualReviewStep
  Awaiting     review_decision
  Since        2026-09-17 09:14:02   (6h 21m ago)
  Times out    2026-09-18 09:14:02   (in 17h 39m)

  Signals
    payment_captured   09:13:58   stripe_webhook   buffered
```

</div>

<div v-click class="mt-6 text-lg text-center">
  Twenty minutes of reading source code, replaced by one command.
</div>

<!--
This is the slide the ops people in the room will remember.

Everything on here is a fact about *waiting*. What it's waiting for, how long
it's been waiting, when it stops waiting, and what it's already heard.

Not one of those five facts can be represented in a status column.

[click] Land it, and mention the siblings quickly: `workflow:instances
--status=awaiting` to see everything currently stopped in your entire system.
That's a genuinely new question you couldn't previously ask.

Note that "buffered" flag on the signal at the bottom — hold that thought, it's
the next section.
-->

---
layout: center
class: px-16
---

# Everything is an event

<div class="grid grid-cols-3 gap-x-8 gap-y-2 mt-8 font-mono text-xs">
  <div>WorkflowStarted</div>
  <div>StepCompleted</div>
  <div>StepFailed</div>
  <div style="color: var(--wf-signal)">WorkflowAwaitingSignal</div>
  <div style="color: var(--wf-signal)">SignalReceived</div>
  <div style="color: var(--wf-human)">StepTimedOut</div>
  <div style="color: var(--wf-sleep)">WorkflowSlept</div>
  <div>WorkflowRetried</div>
  <div>WorkflowCompleted</div>
  <div>WorkflowCompensating</div>
  <div>StepCompensationFailed</div>
  <div>WorkflowFailed</div>
</div>

<div v-click class="mt-10 text-base opacity-75 text-center">
  Dispatched after commit. Six of the twelve are about stopping, waking, or unwinding.
</div>

<div v-click class="mt-4 text-base text-center">
  Your first dashboard is a listener and a table.
</div>

<!--
Twelve events, every transition covered, fired after the database commit so you
never get an event for a thing that then rolled back.

[click] Count the ones about waiting. Half.

[click] And the practical version: you can build "orders stuck for more than four
hours" in an afternoon, because the data is now there to be queried. Previously
that dashboard required you to encode the process rules a second time, in the
dashboard.

`StepTimedOut` is the one to wire up first. It is the alert you never had.
-->

---

# Testing the waiting

````md magic-move
```php {all|1|8|10-12|14-15|17}
it('parks on the payment webhook instead of packing', function (): void {
    $engine = app(WorkflowEngine::class);

    $instance = $engine->start(
        workflowName: 'fulfil_order',
        aggregateId: '4471',
        aggregateType: 'order',
        initialContext: ['order_id' => 4471, 'total_in_cents' => 4_999],
    );

    $engine->advance($instance->id);   // ReserveStockStep
    $engine->advance($instance->id);   // ChargeCustomerStep

    expect($instance->fresh()->status)->toBe('awaiting');

    $engine->signal($instance->id, 'payment_captured', ['risk_score' => 12]);

    expect($instance->fresh()->status)->toBe('in_progress');
});
```
````

<!--
[click] Read the test name. "Parks on the payment webhook instead of packing."

That is a sentence about waiting, and it is now a test. You could not write this
test before. There was nothing to assert against — "the absence of a dispatched
job" is not an assertion, it's a hope.

[click] [click] `advance` drives a step synchronously, so you don't need a queue
worker in your test suite.

[click] Assert it stopped.

[click] Send the signal.

[click] Assert it started again.

And separately — because steps are just classes — you unit test `RiskGateStep`
by newing it up and calling execute with a context. No engine, no database, no
bootstrapping. That was a deliberate design constraint: I have used workflow
engines where testing one branch required standing up the entire runtime, and I
was not going to ship that.
-->

---
layout: section
---

###### Part four

# The bits that will bite you

<!--
This section is why anyone should trust the package.
A talk that only shows the happy path is an advert.
-->

---
layout: center
class: px-16
---

# The webhook is faster than you are

<div class="mt-8 text-base opacity-80 leading-relaxed">
Your step calls Stripe. Stripe processes it, and fires the webhook.<br>
Your webhook handler looks for an instance awaiting <code>payment_captured</code>.<br>
Your step hasn't finished returning yet.
</div>

<div v-click class="mt-8 text-lg" style="color: var(--wf-human)">
  In the old world, that signal is gone forever and the order is stuck.
</div>

<div v-click class="mt-8 text-base opacity-80">
  The engine buffers early signals and consumes them the moment the step parks.
  That's the <code>buffered</code> flag two slides ago.
</div>

<!--
This is a real race and it happens more than you'd think, because Stripe is fast
and your queue is busy.

[click] The old failure is silent. Nothing errors. The webhook returns 200. The
order simply never moves, and you find out on Friday.

[click] The fix has to live in the engine, because it needs to know that a
signal for an instance that isn't yet listening is a *future* signal, not an
invalid one.

Worth saying out loud: this is configurable, and it's on by default, and I'd
leave it on.
-->

---
layout: center
class: px-16
---

# The queue will run your step twice

<div class="mt-8 text-base opacity-80 leading-relaxed">
Every queue you have ever used is at-least-once. That's not a flaw, it's the
only honest guarantee a distributed system can make.
</div>

<div class="mt-8 space-y-3 text-base">
  <div v-click><span class="opacity-60">The engine handles:</span> row locking, stale job guards, illegal transitions throwing rather than corrupting.</div>
  <div v-click><span class="opacity-60">You handle:</span> the idempotency key on the charge.</div>
</div>

<div v-click class="mt-10 text-xl font-bold" style="color: var(--wf-run)">
  The engine can guarantee the step advances once.<br>
  Only you can guarantee Stripe charges once.
</div>

<!--
[click] The engine takes a row lock, checks whether the job it's running is stale,
and refuses illegal transitions with an exception instead of quietly writing a
nonsense status.

[click] But it cannot make your third-party API call idempotent. Nothing can,
except you, at the call site.

[click] This is the most important sentence in this section. Say it clearly.

Any engine that claims to solve idempotency for you is claiming to control an
API it does not own. Be suspicious of that claim, in my package or anyone else's.
-->

---
layout: center
class: px-16
---

# You will deploy in the middle

<div class="mt-8 text-base opacity-80 leading-relaxed">
Order 4471 is asleep for two days. On day one you add a step. On day two it
wakes up.
</div>

<div v-click class="mt-6 text-base" style="color: var(--wf-human)">
  Naïve engines store "step 5". You just made step 5 a different class.
</div>

<div v-click class="mt-8 text-base opacity-80">
  The definition is snapshotted onto the instance when it starts. Instance 4471
  began under Tuesday's process and it will finish under Tuesday's process.
</div>

<div v-click class="mt-8 text-base opacity-70">
  Which means the honest question isn't "how do I migrate in-flight instances?"<br>
  It's <span class="font-bold">"how long am I willing to run two versions of this process at once?"</span>
</div>

<!--
[click] This is the bug that bites everyone who builds their own version of this
over a weekend. Storing an integer index into an array you're going to edit.

[click] The snapshot fixes the correctness problem.

[click] But it doesn't fix the organisational problem, and I want to be straight
about that. If your process has a two-day sleep in it, then every deploy creates
a fleet of instances running the old process alongside the new one.

That's not a flaw in the engine. That's just true of any long-running process,
including the manual version you have today — you've just never been able to see
it before. This makes it visible, and visible problems are the good kind.
-->

---
layout: center
class: px-16
---

# Compensation is code, and code fails

<div class="mt-8 text-base opacity-80 leading-relaxed">
The order is rejected. We try to refund. The refund throws.
</div>

<div v-click class="mt-8">

```php
Event::listen(StepCompensationFailed::class, function ($event): void {
    Alert::urgent("Order {$event->instanceId} needs a human. Now.");
});
```

</div>

<div v-click class="mt-10 text-xl font-bold" style="color: var(--wf-human)">
  The last compensating step in every system is a person with a phone.
</div>

<!--
You cannot automate your way out of the bottom of this. At some point money has
moved and the system cannot move it back, and the correct response is to page
someone.

[click] What the engine gives you is that this is an *event*, immediately, with
the instance id, rather than a discrepancy someone finds in a reconciliation
report three weeks later.

[click] Say this one and pause. It's the honest ending of the reliability story
and people appreciate not being sold a fantasy.
-->

---
layout: center
class: px-20
---

# When not to use this

<div class="mt-8 space-y-5 text-base">
  <div v-click><span class="font-bold">Nothing waits?</span> <span class="opacity-70">It's a job. Use a job. Jobs are great.</span></div>
  <div v-click><span class="font-bold">Three steps, 200ms, always finishes?</span> <span class="opacity-70">It's a pipeline. Use a pipeline. I wrote a package for that too, and this isn't it.</span></div>
  <div v-click><span class="font-bold">CRUD?</span> <span class="opacity-70">Please, no.</span></div>
</div>

<div v-click class="mt-12">

<div class="text-lg font-bold">Two questions:</div>

<div class="mt-3 text-base opacity-80">Does it stop? &nbsp;·&nbsp; Does anyone ever ask "where has it got to?"</div>

<div class="mt-3 text-base">Two yeses, and it's a workflow. Otherwise it isn't.</div>

</div>

<!--
Be genuinely dismissive of over-application here. The fastest way to make people
distrust a tool is to watch someone use it for everything.

[click] Jobs are good. Most things are jobs.

[click] The pipeline line gets a laugh and it's true — laravel-flows is for
synchronous multi-step logic that runs to completion. Different problem entirely.
If it never stops, you don't need any of the machinery in this talk.

[click] Obviously.

[click] And then the two-question test. That's the thing to write down if you're
writing anything down.
-->

---
layout: center
class: px-16
---

# Order #4471, one more time

<div class="mt-10">
  <Timeline scale="log" labels notes height="4rem" />
</div>

<div v-click class="mt-12 text-center text-lg">
  Every stop now has a name, a timeout, a row in a table,<br>
  and an answer to "what are you waiting for?"
</div>

<!--
Bring it home. Same timeline, but now the three big gaps have names underneath
them — await payment_captured, await review_decision, sleep 48h.

Those three annotations are the entire difference between this talk's before and
after. The work didn't change. The waiting got written down.

[click] Then the line, and straight into the close.
-->

---
layout: statement
class: text-center
---

<div class="text-3xl leading-relaxed">
  The waiting was always the process.
</div>

<div class="text-3xl mt-6 leading-relaxed opacity-80">
  We just never wrote it down.
</div>

<!--
Full stop. Don't add anything after this sentence. Let it sit, then move to the
links slide for questions.
-->

---
layout: end
---

<div class="end-columns">

<div>

###### Thank you

<div class="mt-7 space-y-4 text-sm">
  <div>
    <div class="kicker mb-1">Package</div>
    <code>juststeveking/workflow-engine</code>
  </div>
  <div>
    <div class="kicker mb-1">Walkthrough</div>
    juststeveking.com/articles/building-an-order-fulfilment-workflow
  </div>
</div>

<div class="mt-8 text-lg" style="color: var(--jsk-muted)">
  Any Questions?
</div>

</div>

<div>
  <div v-click class="kicker mb-4">Some questions I am expecting</div>
  <div class="space-y-3.5">
    <v-clicks>
          <div>
      <div class="font-sans text-[0.8rem] font-600" style="color: var(--jsk-heading)">Isn't this just Temporal?</div>
      <div class="text-xs mt-0.5" style="color: var(--jsk-muted)">Temporal is a separate server and runtime. This is two tables and the queue you already run.</div>
    </div>
    <div>
      <div class="font-sans text-[0.8rem] font-600" style="color: var(--jsk-heading)">Why not a state machine package?</div>
      <div class="text-xs mt-0.5" style="color: var(--jsk-muted)">They model the transitions, not the pause. You end up back at a cron job.</div>
    </div>
    <div>
      <div class="font-sans text-[0.8rem] font-600" style="color: var(--jsk-heading)">Parallel steps, fan-out?</div>
      <div class="text-xs mt-0.5" style="color: var(--jsk-muted)">Linear today. A correct linear engine beats a racy parallel one.</div>
    </div>
    <div>
      <div class="font-sans text-[0.8rem] font-600" style="color: var(--jsk-heading)">Performance?</div>
      <div class="text-xs mt-0.5" style="color: var(--jsk-muted)">A write and a job per step boundary. Not for a process that runs in 200ms.</div>
    </div>
    <div>
      <div class="font-sans text-[0.8rem] font-600" style="color: var(--jsk-heading)">Can I skip the queue?</div>
      <div class="text-xs mt-0.5" style="color: var(--jsk-muted)">Tests advance steps synchronously. In production the design assumes a worker process.</div>
    </div>
    </v-clicks>
  </div>
</div>

</div>

<!--
Land the thank-you and the links first. The right column starts empty — only
click a question up when it's actually asked, or when the room goes quiet and I
want to prime it. They don't have to come out in order.

Full versions, if the line on screen isn't enough:

[click] "How is this different from Laravel Workflow / Temporal?"
Temporal is excellent and it's a separate server, a separate language runtime,
and an operational commitment. This is two tables and your existing queue. If you
already run Temporal, keep running Temporal.

[click] "Why not a state machine package?"
State machine packages model the transitions. They don't model the *pause* —
there's no durable place for "I am stopped, waiting for this named thing, until
that time". You end up back at a cron job.

[click] "What about parallel steps / fan-out?"
Steps are linear today. Fan-out is the obvious next thing and I'd rather ship a
correct linear engine than a racy parallel one.

[click] "Performance?"
Every step boundary is a database write and a queued job. If your process runs in
200ms this is the wrong tool — see the "when not to use this" slide.

[click] "Can I use it without the queue?"
`workflow:advance` runs a step synchronously, which is how the tests work. But in
production, no — the whole design assumes a worker.
-->
