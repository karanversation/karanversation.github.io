---
layout: post
title: "TradeCops - A Generic tool for Parameter based Visualization"
categories: [prototypes]
date: 2014-01-29
---

Data is important to all of us.

But what's more important is the WAY we see it.

TradeCops was built for a specific business use case, but the design and code are highly generic - which means all it needs to port it onto a totally unrelated business case is a few changes in the configuration files.

Well, this is how enterprise systems work.

Our business case was for **Trade Compliance and Risk Governance**. Compliance monitor trading activity of their clients through a monitoring system. Once in a while, the system shows anomalous trading behaviour. The judgement is based on automated algorithms fed to the monitoring engine.

The question for Compliance now, is whether this really is an abnormal activity? It might be -

- a perfectly sensible move (in case Apple fires it's CEO, the stock is bound so tank. So if client is selling the stocks madly, it's stupid to blame him for abnormal trades),
- or something fishy (the market sentiment is neutral, but the client is selling like millions of stocks!

The way to do this is to manually check out information online and through firm proprietary systems. And once you have the intel from a dozen different sources, use your head to come up with a sensible boolean decision.

Too bad.

TradeCops asks Compliance to 'plug in' their data sources, select what they want to visualize, how, and for what (we'll give you can example soon), and viola - a compelling visualization in seconds.

![TradeCops Visualization](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjcP40TjyJpPRB6ds-AlsR7RvUCVdLn3xGfQMkrMiuWtVOiCPtQ2oN0KDLzejN5nddcS833f0oLemnhffVpv3a327NppNPvu4MR9BWpjQkjLVgSr_dmchlx3nUWo5Al3QGfKRV-smew9mj2/s1600/TradeCops_2.jpg)

Yeah. This is what I was talking about.

![TradeCops Chart](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAZcZUwZU_Ad9IQAfQ0lNi_lSky7YZjjg4NrtiVJrRHK9Ga1W70kHs9P9gpt_sG-3ePFrKLoUCgeQmRrurWK_HtJgCEjaUXaRK0GBle8EiEQppdrKiEKy9ZL6TOX3b2Eyx_TcOsWzW9opq/s1600/Tradecops.jpg)

And the specifications for this chart.

Or else advanced features like 'Saved template loading' (which I'll illustrate later)

Now you are free to provide specifications according to your requirement, and we can visualize almost anything*

*Information processed and visualized by us is in tabular format (Database table, CSV files, etc)

Another snapshot of Tradecops flaunting it's UI.

![TradeCops UI](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEix7_2BAO0w2Hw0GuJrEKb5LAz_k5kSkajrxJn8T4BRFCTRLJkwxxNkxwSp5j31n8LsaHi5QHrzhyw3goxAXrAnA_7ez_NcfYWRZioAsqsBoJmdht6eOCSqeYDh_OzCKQl42bnuBxLgL-3C/s1600/Tradecops.jpg)

My next blog talks about the art behind the scenes. In simpler words - the underlying architecture, technologies involved, introduction to a design pattern implemented and lastly, the sexy UI code structure.
