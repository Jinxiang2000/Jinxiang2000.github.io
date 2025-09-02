---
title: "Building LazyMind"
date: 2025-09-02
draft: false
description: "When AI Helps You Actually Get Things Done"
categories: ["LLM"]
tags: [“LLM"，”Software Development"]
math: true
---


I've always been terrible at planning. You know that feeling when you have a vague goal like "I should learn cooking" or "maybe I'll start working out," but then you just... don't? I built LazyMind because I was frustrated with every planning app expecting me to already know what I wanted to do and how to break it down.

The idea was simple: what if I could just tell an AI what I wanted to achieve, and it would figure out the actual steps? Not another todo app with fancy colors, but something that actually understands what you're trying to accomplish and helps you get there.

## How It Works

The core idea is pretty straightforward. You tell LazyMind something like "I want to prepare for software engineering interviews," and instead of giving you generic advice, it analyzes what that actually means for you specifically. Then it breaks it down into a roadmap with concrete steps you can actually follow.

The user experience I was aiming for is dead simple: speak or type your goal, get a plan, start working. No complex setup, no learning a new system, just results.

## The Technical Side

I built LazyMind using Next.js 15 with React and TypeScript because honestly, it's what I know best and it gets the job done. Tailwind CSS for styling because I'm not a designer and it helps me make things that don't look terrible. The whole thing runs on Vercel because deployment should be easy.

For the backend, I went with Supabase. I know everyone talks about building your own APIs, but Supabase gives you PostgreSQL, real-time subscriptions, auth, and row-level security out of the box. For a project like this where I wanted to focus on the AI logic rather than infrastructure, it was perfect.

The database schema was actually more interesting to design than I initially thought. The core challenge was storing both the user's original goals and the AI-generated plans in a way that supports iterative refinement:

```sql
-- Core tables for the planning system
CREATE TABLE goals (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id uuid REFERENCES auth.users(id),
  original_text text NOT NULL,
  analyzed_intent jsonb, -- AI's understanding of the goal
  complexity_score integer,
  created_at timestamp DEFAULT now()
);

CREATE TABLE tasks (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  goal_id uuid REFERENCES goals(id),
  title text NOT NULL,
  description text,
  estimated_duration integer, -- in minutes
  order_index integer,
  status text DEFAULT 'pending',
  created_at timestamp DEFAULT now()
);

CREATE TABLE subtasks (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  task_id uuid REFERENCES tasks(id),
  title text NOT NULL,
  description text,
  estimated_duration integer,
  order_index integer,
  status text DEFAULT 'pending',
  created_at timestamp DEFAULT now()
);
```

The key insight was storing the AI's intent analysis as JSONB. This lets me query and update specific parts of the analysis without rebuilding everything from scratch. Plus, PostgreSQL's JSONB support is really good for this kind of semi-structured data.

For the AI part, this was the real challenge. I started with OpenAI's GPT-4 because it's genuinely good at understanding what people mean when they say vague things like "I want to get better at programming." But GPT-4 is expensive, so I also integrated DeepSeek as a backup option for simpler tasks. The tricky part was making the AI understand context. When someone says "I want to learn programming," that could mean anything. Are they a complete beginner? Do they want to change careers? Are they already technical but learning a new language? Getting this right required a lot of experimentation with prompt engineering.

Here's what I learned: the key isn't just asking the AI to generate a plan, but asking it to think through what the person actually wants to achieve first.

For example, instead of immediately jumping to "here's how to learn React," I prompt the AI to first figure out:
- What does this person already know?
- What's their timeline?
- What's their end goal?
- What resources do they have?

Only then does it generate the actual plan. This two-step process makes a huge difference in the quality of the output.

The second major feature is breaking down tasks into subtasks. This was harder than I expected because you need to find the right level of granularity. Too high-level and people get stuck, too detailed and it becomes overwhelming.

I spent a lot of time getting this right. The AI needs to understand that "Set up a development environment" should become subtasks like "Install VS Code," "Install Node.js," "Create your first project folder," etc. Each subtask needs to be something you can actually do in one sitting, usually 15 minutes to 2 hours.

## The Features That Actually Matter

The first thing I built was voice input. I know it sounds gimmicky, but there's something about speaking your goals out loud that makes them feel more real. Plus, it's faster than typing when you're just getting started. The voice recognition uses the browser's built-in speech API, which is actually pretty good these days.

![image-20250902001807627](/img/learning/image-20250902001807627.png)

AI analyzes what you want to do and creates a step-by-step roadmap. But here's the thing I learned: people don't follow linear plans. Life gets in the way, priorities change, you realize some steps are harder than expected.

So I built in a feedback system. When you complete a task or get stuck, you can tell the AI what happened, and it adjusts the remaining plan. This turned out to be way more useful than I initially thought.

I also added a simple motivation system. When you complete tasks, the AI generates encouraging messages that acknowledge what you actually accomplished. It's not groundbreaking, but it helps with momentum.

## Authentication and Calendar Integration

For authentication, I went with Google OAuth because most people already have Google accounts and I wanted to integrate with Google Calendar eventually. Supabase's auth system made this pretty straightforward - it handles the OAuth flow and automatically creates user records.

![image-20250902003153401](/img/learning/image-20250902003153401.png)

Once users confirm their task roadmap, the system automatically syncs all the tasks to their Google Calendar. It's not just dumping tasks randomly - the system creates properly formatted calendar events with descriptions, estimated durations, and logical sequencing.

```typescript
// Simplified version of the calendar sync logic
async function syncTasksToCalendar(confirmedRoadmap: Task[], userCalendar: CalendarClient) {
  for (const task of confirmedRoadmap) {
    const event = {
      summary: task.title,
      description: `${task.description}\n\nEstimated duration: ${task.estimated_duration} minutes`,
      start: { dateTime: task.scheduled_time },
      end: { dateTime: addMinutes(task.scheduled_time, task.estimated_duration) },
    };
    
    await userCalendar.events.insert({
      calendarId: 'primary',
      requestBody: event,
    });
  }
}
```

The next big feature I'm working on is intelligent scheduling using operations research algorithms. Instead of users having to manually schedule when to work on tasks, the system will analyze their existing calendar and automatically find optimal time slots for each task block. The goal is to help people make better use of their fragmented free time - those 30-minute gaps between meetings that usually get wasted.

## Performance and Real-World Usage

One thing I learned quickly is that calling GPT-4 for every single interaction gets expensive fast. So I implemented a simple caching system and use DeepSeek for less complex operations. The app typically responds in under 2 seconds for plan generation, which feels snappy enough.

The architecture is pretty straightforward: Next.js API routes handle the LLM calls, Supabase stores everything, and I use Vercel's edge functions for the real-time stuff. The nice thing about this setup is that it scales automatically - I don't have to worry about server management or database scaling.

For state management, I kept it simple with React hooks and localStorage for offline persistence. Since most interactions are short-lived (generate plan, review, start working), I didn't need anything complex like Redux.

I've been testing it with friends and family, and the feedback has been pretty positive. People actually use it, which is always a good sign. The most common feedback is that it helps them get started on things they've been putting off.

The calendar sync feature has been particularly well-received. There's something powerful about seeing your abstract goals actually blocked out in your calendar as real, scheduled events. It makes the commitment feel more concrete.

## What I Learned

Building LazyMind taught me a lot about working with AI in practice. The biggest lesson was that prompt engineering is actually a lot like regular programming - you need to be precise, test edge cases, and iterate based on results.

I also learned that user context is everything. The same goal means completely different things to different people, and getting the AI to understand that nuance made all the difference in the quality of the plans it generates.

The other big lesson was about graceful degradation. AI services fail, models get rate limited, and users have inconsistent internet connections. Building fallbacks and handling errors gracefully turned out to be just as important as the core AI functionality.

## What's Next

I'm working on a few major improvements:

**Document Integration**: Right now, LazyMind generates plans based on general knowledge. I'm building a system where users can upload PDFs, articles, or other resources, and the AI will create plans based on that specific content. This involves some vector database work and retrieval-augmented generation, which is proving to be an interesting technical challenge.

**Intelligent Operations Research Scheduling**: This is the big technical challenge I'm tackling next. Right now, users can sync their task roadmaps to Google Calendar, but I want to flip that around. The system should analyze your existing calendar, understand your schedule patterns, and automatically insert task blocks into optimal time slots.

The plan is to use operations research algorithms (think constraint satisfaction and optimization) to solve the scheduling problem. Variables include task duration, complexity, user energy levels at different times, existing commitments, and even factors like commute time. The goal is to turn those random 20-30 minute gaps in your calendar into productive work sessions.

**Pattern Learning**: Eventually, I'd like the AI to learn from patterns across all users (while preserving privacy) to make better recommendations. If thousands of people successfully learned programming using similar approaches, that information should help newcomers.

## Why This Matters

I think we're at an interesting point with AI where it's powerful enough to be genuinely useful but still needs thoughtful implementation to work well. LazyMind isn't trying to replace human planning entirely - it's trying to be the best possible starting point.

The goal was never to build the perfect planning app. It was to build something that helps people take the first step toward their goals. And based on the feedback I've gotten, it seems to be working.

If you're thinking about building AI products, my advice is to start with a real problem you have personally. Don't build AI for the sake of AI - build it because it solves something that's actually annoying or difficult. And be prepared to spend a lot of time on prompt engineering. It's more important than you think.

---

**Technical Details**: Built with Next.js 15, React, TypeScript, Supabase, and Tailwind CSS. AI integration uses OpenAI GPT-4 with DeepSeek as a fallback. Authentication via Google OAuth with calendar permissions for future features. Deployed on Vercel.

