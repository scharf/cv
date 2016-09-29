## 2009-2013 Employed as Principal Technologist at Wind River

2009-2013 I have been working on Eclipse plugins, UI  and the middleware
for large scale data processing and visualization of performance
data for embededed system (which are written in C/C++). The project
used the eclipse Modeling framework (EMF) and several textual
DSLs (Domains pecific languages) and form based configurators that described the embedded system
as well as the instrumentation requred to measure different
aspects of performance of the software as well as tha hardware.
Those DSLs (Domains pecific languages) have been used to configure
the UI at runtime and generated instrumentations for the C code.
The key design objective pf the project was to create a framework 
to flexibly react to a class of requirements on performance analysis.

I was the lead Java developer and architect of the system. The
configuration 


Early 2009 I decided it is time to become an employee and **I became a
member of the CTO office** reporting to the CTO ([Tomas Evensen]). 
In addition to the role as architect, I spend
most of my time on one CTO office project where we created a runtime
analysis system for embedded systems. Wind River had a set of
independent analysis tools for different operating systems using
different technologies for instrumentation and target communication
(partly because they came for different acquisitions). Although it
started as research project between me and another Principal
Technologist (Felix Burton), the decision was made quite early to use
this as a pilot project for agile development (and scrum in
particular) with 8 people. This added a lot of non-technical
challenges and I learned a lot about the good and bad parts of agile.

On the technical side, **I was responsible for java part and the
(eclipse based) visualization** and (with Felix) for the overall
architecture and design of the system. In that position I had no
management responsibilities but I did a lot of coding and pair
programming with some of the members of the team.

* co-designed a concise set of rules and operations for
  collecting and aggregating runtime data
* implemented a data processing engine for aggregating
  data - the engine was designed in multiple stages in
  different processes and on different systems (partly
  on the target, partly on the host)
* invented a value data structure that was a transactional,
  copy on write, delayed notification, efficient update,
  memory efficient and thread save (called IValue)
* configurable multistage data processing engine
  (interpreter pattern)
* template based configurable UI with DSL (Domain Specific Language)
  in [Xtext] with data binding to IValue which was (partly) driven
  by the incoming (meta) data
* code generator for several data aggregators that created C-code
  and virtual machine code
* extensible parser framework for a set of textual DSLs -
  allowed to plug in sub parsers in other DSLs
