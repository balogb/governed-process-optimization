# Example input: intake-call transcript (synthetic)

Fictional intake conversation between a process analyst and an IT operations lead about an application
**access-provisioning** process. Names, company, systems, and numbers are all invented for
demonstration - this is not based on any real organization.

---

**Analyst:** Walk me through what happens today when someone needs access to one of the internal
applications.

**IT ops lead (Marco):** Right now a person emails the service desk saying "I need access to System X."
We send back a form, they fill in which roles they think they need, their manager emails an approval,
then the application owner reviews it, then our security team reviews it for segregation-of-duties
conflicts, and finally one of my engineers provisions it by hand in the target system.

**Analyst:** How many approval steps is that?

**Marco:** Four before provisioning - manager, app owner, security, and then there's a data-governance
sign-off that got added at some point. Honestly I'm not sure that last one does anything different from
the security review, but nobody's ever taken it out.

**Analyst:** How long does a request take end to end?

**Marco:** About six business days on average. Some of that is people sitting in inboxes. And roughly a
quarter of requests come in asking for the wrong roles, so they bounce back. We handle around two
hundred access requests a month across all the apps.

**Analyst:** Where's the risk?

**Marco:** Two things worry me. People over-request "just in case," and because it's manual, they
sometimes get more access than they should - that's a real audit exposure since some of these systems
hold financial and personal data. And we don't have a clean way to review access after the fact - once
you're in, you're in.

**Analyst:** What would good look like?

**Marco:** Requests tied to predefined role bundles so people can't over-ask, automatic
segregation-of-duties checking, provisioning without manual keying, and a record we can actually audit.
Get six days down to one or two.

**Analyst:** Who owns this end to end today?

**Marco:** Nobody, really. Service desk takes the request, app owners own their app, security owns the
policy, my team does the provisioning. I'd champion fixing it, but I can't direct the security or
app-owner teams.

**Analyst:** Anything driving urgency?

**Marco:** We have an access-review audit coming later this year, and the manual trail won't hold up
well. Not a hard deadline yet, but it's coming.
