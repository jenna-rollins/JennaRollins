# 02 · Super Hearing — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. Last session you built a context file and met the
company.

You still do not know what actually went wrong.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

Is there an SLA outlined for how quickly pings should be acknowledged?

### 2.

Check what share of takes happen between 45 and 60 seconds, and what the 90s era looked like.

### 3.

Are their certain responders who have increased misses since the change to 90s?

### 4.

Compare these four responders’ ping volume week by week to see whether the drop was gradual. Also check whether their availability or any ticket mentions explain the misses.

### 5.

Do responders have a protocol on how and when to respond to pings? For example, should responders be responding to pings before getting dressed/ ready?

### 6.

No thank you. Another question, do the handlers tell the responders to keep their phones near by at all times? Also, do any of the responders use apple watches or an android watch to receive pings?

### 7.

Have we confirmed if the responders phones are on the updated software/ applications? If we have this information, put it in table format

### 8.

Draft a table that provides similarities, discrepancies, timestamps, and responders for the following tickets: 3048, 3077, 3078, 3052, 3113, 3123

### 9.

Can we review the pings before and after 4.2? Are we sending the same pings to multiple responders in order to assign the person who can get there the fastest?

### 10.

Prior to August 12th, what was each responders average acknowledgement time on a ping?

### 11.

Have we confirmed in the code that the rate is truly set to 60s?

### 12.

Since 4.2, provide a breakdown of the time it takes push_to_device  to return? Is there any sign that the pushes have been slow causing a responder’s phone to show the offer later than the clock started, so the usable window could be shorter than 60 seconds. The data can’t show this. Was there a change to push_to_device in 4.3

### 13.

Ask engineering for push-service delivery logs, meaning send time and device receipt time per ping, from 29 Jun to 6 Sep. With them I could compare delivery delay before and after 12 Aug, and check whether the four quiet responders’ pings arrive late.

