[goto Chinese edition (中文版)](./Readme_Chinese.md)

# The Pure Function Pipeline Data Flow v3.0
# ---- the Grand Unified Programming Theory

Copyright © 2018 Lin Pengcheng. All rights reserved.

## Table of Contents
- [My and Other People's Related Views](#My-and-Other-Peoples-Related-Views)
- [Summary](#Summary)
- [Basic construction method](#Basic-construction-method)
  - [1. Pipeline Component](#1-Pipeline-Component)
  - [2. Branch](#2-Branch)
  - [3. Feedback circuit (reflow, whirlpool, recursive)](#3-Feedback-Circuit)
  - [4. shunt (concurrent, parallel)](#4-Shunt)
  - [5. Confluence(reduce)](#5-Confluence)
- [Code Example](#Code-example)
- [Classical Model](#Classical-Model)
  - [Warehouse/Workshop Model](#Warehouse-Workshop-Model)
    - [The unification of `programming technology` and `system architecture`](#The-unification-of-programming-technology-and-system-architecture)
    - [The unification of `single-threaded`, `multi-threaded`, `asynchronous` and `distributed`](#The-unification-of-single-threaded-and-multi-threaded-and-asynchronous-and-distributed)
      - [The unification of `Microservice` and `Intelligent-thread`](#The-unification-of-Microservice-and-Intelligent-thread)
    - [The unification with `Information System Integration Model`](#The-unification-with-Information-System-Integration-Model)
    - [The unification with `Microkernel Architecture`](#The-unification-with-Microkernel-Architecture)
    - [The unification with `AOP`](#The-unification-with-AOP)
    - [The unification with `Event-driven Architecture`](#The-unification-with-Event-driven-Architecture)
    - [The unification with `Computer Hardware Architecture`](#The-unification-with-Computer-Hardware-Architecture)
    - [The unification with `Integrated Circuit System`](#The-unification-with-Integrated-Circuit-System)    
    - [The unification with `Programming Language Platform`](#The-unification-with-Programming-Language-Platform)    
    - [The unification with `Clojure Web Application Model`](#The-unification-with-Clojure-Web-Application-Model)    
    - [The unification with `Lifecycle Management`](#The-unification-with-Lifecycle-Management)    
    - [The unification with `Other Models`](#The-unification-with-Other-Models)    
    - [Warehouse/Workshop Model Summary](#Warehouse-Workshop-Model-Summary)    
- [The difference between it and others](#The-difference-between-it-and-others)
  - [Disadvantages of FP and OO](#Disadvantages-of-FP-and-OO)
  - [The difference between it and middleware](#The-difference-between-it-and-middleware)
  - [The difference between it and Rx](#The-difference-between-it-and-Rx)
  - [The difference between it and traditional unix-like pipe operator in FP language](#The-difference-between-it-and-traditional-unix-like-pipe-operator-in-FP-language)
- [Basic quality control](#Basic-quality-control)
- [Tao](#Tao)
- [Killer Application](#Killer-Application)
  - [Software Design and Develop Automation (SDDA)](#Software-Design-and-Develop-Automation)
- [Great Historical Significance](#Great-Historical-Significance)
- [Postscript](#Postscript)
- Appendix:
  - [(Chinese) Simplicity and Unity ---- Grand Unified Theory, Lisp(Clojure) and Pure Function Pipeline Dataflow](doc/Simplicity_and_Unity.md)
  - [Clojure is a FP based on RMDB.](doc/Clojure_is_FP_based_on_RMDB.md)
  - [Everything is RMDB.](doc/Everything_is_RMDB.md)
  - [Implement relational data model and programming based on hash-map (NoSQL)](doc/relational_model_on_hashmap.md)
  - [Markdown Literary Programming that don't break the syntax of any programming language](doc/markdown_literary_programming.md)
  - [Other Articles Table of Contents](#Other-Articles-Table-of-Contents)

----
  
## My and Other Peoples Related Views

```
Keep it Simple and Unified.
        ---- Lin Pengcheng

NASA’s 10 rules for writing mission-critical code: 
1.Restrict all code to very simple control flow constructs.
        ---- Gerard J. Holzmann, NASA JPL lead scientist.
        
Minimize control flow complexity and "area under ifs", 
favoring consistent execution paths and times over "optimally" avoiding unnecessary work.
        ---- John Carmack

Clojure Aphorism: A tangled web of mutation means any change to 
your code potentially occurs in the large. 
        ---- The Joy of Clojure (2nd Edition, Chapter 10)
        
Bad programmers worry about the code. 
Good programmers worry about data structures and their relationships.
        ---- Linus Torvalds
        
Data dominates. If you’ve chosen the right data structures and organized things well, 
the algorithms will almost always be self-evident. 
Data structures, not algorithms, are central to programming. 
        ---- Rob Pike
        
It’s better to have 100 functions operate on one data structure 
than 10 functions on 10 data structures.        
        ---- Alan Perlis
             the first recipient of the Turing Award (1966)
             A founding father of Computer Science as a separate discipline
             
Show me your flowcharts and conceal your tables, 
and I shall continue to be mystified. Show me your tables, 
and I won’t usually need your flowcharts; they’ll be obvious.
        ---- Fred Brooks, Turing Award (1999), The Mythical Man-Month
           
Even the simplest procedural logic is hard for humans to verify, 
but quite complex data structures are fairly easy to model and reason about. 
...
Data is more tractable than program logic. It follows that where you see a choice 
between complexity in data structures and complexity in code, choose the former. 
More: in evolving a design, you should actively seek ways to shift complexity from code to data.
        ---- Eric Steven Raymond, The Art of Unix Programming, Basics of the Unix Philosophy
        
Metaphors for a Richer Understanding of Software Development.
        ---- The most valuable chapter of "Code Complete": Chapter 2
        
Principles-based are better than rules-based.
        ----International Accounting Standards        
```

## Summary

Using the input and output characteristics of pure functions, pure functions are used as pipelines.
Dataflow is formed by a series of pure functions in series.
A dataflow code block as a function, equivalent to an integrated circuit element (or board)。
A complete integrated system is formed by serial or parallel dataflow.

Can also be said, Data and logic are strictly separated, 
Element level separation of data and logic, data stream processing.

```clojure
(defn f [[evens odds total amax amin] x]
  (let [[evens odds] (cond 
                       (even? x) [(inc evens ) odds]
                       (odd? x)  [evens (inc odds)]
                       :else     [evens odds])
        total (+ total x)
        amax  (max amax x)
        amin  (min amin x)]   
     [evens odds total amax amin]))

(reduce f [0 0 0 ##-Inf ##Inf] [5 6 8 -3 -9 11 156 6 7])

;;[4 5 187 156 -9]
```

For me, programming is the process of designing a data model that is simple and fluent in manipulation. 
More than 80% functions of my project is `->>` threading macro code block, 
each step is simple, verifiable, replaceable, testable, pluggable, extensible,
and easy to implement multithreading. 
The clojure threading macro provides language-level support for PurefunctionPipeline&Dataflow.

The sea sails by the helmsman and the programming moves toward the data. Initial state, final state, 
the shortest linear distance between two points. Simplicity is the root of fast, stable and reliable. 

```
Those who are good at war have no surprising victory, no reputation for wisdom, no honor for courage.
        ---- Sun Wu, The Art of War
             Famous Chinese military and politician, 
             sage of military science, 
             ancestor of Eastern military science
```

The design philosophy of industrial pipeline and pure function pipeline data flow is the same. 
In essence, they are: "Towards the goal, Step by step, every step moves forward to the final goal 
until the final goal is reached." Therefore, its success is inevitable, not surprising, the process is a simple repetition.
After using this method proficiently, it is a simple and repeated boring technique. 
This is the simplicity and repetition pursued by large industrial production lines.

## Basic construction method

### 1 Pipeline Component

A ->> block function is equivalent to an integrated circuit component (or board).
A series of functions in a ->> block can only have one function with side effects 
and can only be at the end. 
In addition, we must pay attention to the data standardization work, 
verify the data at the entrance and exit, 
and run at full speed in the middle, 
which is simple, smooth, stable and efficient.

In the clojure language, it is recommended that the function be designed as 
a single-parameter function with a hash-map type. 
Like most functions in the R language, you can design many named parameters with default values, 
which are highly scalable. 
In addition, clojure has many core functions for operating hash-map, it's easy to operate,
not only it may don't write parentheses when using ->> macro, 
it can be integrated that parameter formation, verification, transformation and serial pipeline functions, 
It is also convenient to deconstruct in clojure. 
which is as convenient as multi-parameter functions.

```clojure
(defn f [x]
  (->> x
       f1
       f2))
```

```clojure
(defn f [{:keys [x y] :as m}]
  (->> x
       (f1 y ,)
       f2))
```

### 2 Branch

A (cond) or (if) block as a function.

```clojure
(defn f [x]
  (cond
    (= x 1) (f1)
    (= x 2) (f2)
    :else   (f3)))
```
```clojure
(defn f2 [x y]
  (-> (> x 2)
      (and , (< y 6))
      (if , 25 30)))
```
```clojure
(defn path-combine [s1 s2]
  (cond
    (string/starts-with? s2 "/") 
      s2
    (not (string/ends-with? s1 "/"))
      (-> (string/split s1 #"[\\/]")
          butlast
          (#(string/join "/" %))
          (str , "/")
          (path-combine , s2)) 
    :else  
      (-> (string/join "/" [s1 s2])
          (string/replace ,  #"[\\/]+" "/")))) 
```

### 3 Feedback Circuit

Feedback circuit (reflow, whirlpool, recursive): 
A tail recursive function is equivalent to a feedback circuit. 

Note: The map is batch processing. it can be regarded as similar to a queue of tourists. 
Repeating the ticket checking action at the entrance is a forward action, 
not feedback or reflow.

```clojure
(defn f [i]
  (if-not (zero? i)
    (f1)
    (-> i dec recur)))
```

### 4 Shunt

Shunt（concurrent，parallel）,
For example: data partitioning, parallel processing

```clojure
(->> data
     (partition n ,)
     (pmap f ,))
```
```clojure
(->> [pipe-f1 pipe-f2 pipe-f3]
     (pmap #(% data) ,))
```

### 5 Confluence

Confluence（reduce）: reduce the result of the shunt

```clojure
(->> data
     (partition n ,)
     (pmap f1 ,)
     (reduce f2 ,))   
```

## Code example

### Code example 01

```clojure
;Traditional expression, chaotic logic, unreadable.
(if (and (> x1 x2)
         (or (< x3 x4) 
             (and (or (> y1 y2) 
                      (< y3 y4))
                  (not= x5 x6)))
         (keyword? x7)) 
  :t
  :f)

;Pure Function Pipeline Dataflow
;Unrestricted expression, just read in order. 
;Closer to the order of execution of the machine.
(->  (> y1 y2)
     (or  , (< y3 y4))
     (and , (not= x5 x6))
     (or  , (< x3 x4))
     (and , (> x1 x2))
     (and , (keyword? x7))       
     (if  , :t :f))
```

### Code example 02

```clojure
(def data
  {:a [[:b :c :d]
       [:e :f :g]
       [:h :i :j]]
   :k [[:l :m :n]
       [:o :p :q]
       [:r :s :t]]})

(defn f1 [[k v]]
  (let [[h & t] v
        f   (fn [x] (mapv #(vector :td %) x))
        tds (map #(->> % f (into [:tr] ,)) t)]
     (->> (f h)
          (into [:tr [:td {:rowspan (count v)} k]] ,)
          (conj tds ,))))

(->> data
     (reduce #(->> %2 f1 (into %1 ,)) [:tbody] ,)
     (conj [:table] ,)
     hiccup/html)

```

<table>
    <tbody>
       <tr><td rowspan=3>a</td>
           <td>b</td>
           <td>c</td>
           <td>d</td></tr>
       <tr><td>e</td>
           <td>f</td>
           <td>g</td></tr>
       <tr><td>h</td>
           <td>i</td>
           <td>j</td></tr>
       <tr><td rowspan=3>k</td>
            <td>l</td>
            <td>m</td>
            <td>n</td></tr>
       <tr><td>o</td>
            <td>p</td>
            <td>q</td></tr>
       <tr><td>r</td>
            <td>s</td>
            <td>t</td></tr>
    </tbody>
</table>

## Classical Model

```
The true sign of intelligence is not knowledge but imagination (analogy).
      ---- Albert Einstein
```

Analogy is an application of algebraic thinking. An analogy may not be established in the real world, 
but it must be established in the virtual software world,
it is very easy to map an old model to a new model and upgrade according to the standard specifications of the new model.
In management, it is called "merger by absorption". 

This is a typical application of the philosophy of the `Tao` and the `Grand Unified Theory`.

- data-flow is current-flow, function is chip, thread macro (->>, -> etc.) is a wire, and the entire system is an integrated circuit that is energized.
- Data (flow) is raw material (flow), pure function is machine, the thread macro (->>, -> etc.) is the conveyor belt, and the entire system is a large industrial pipeline.
- Communication modes, data signals and control signals (annotations or metadata) flow through the pipeline.
- The compiler is essentially a data transformation, starting with the source code, through a series of pure function pipeline transformation optimization, up to the machine code. It's very easy to insert enhanced optimizations or features, and parallel compilation is also very simple.
- Urban water network
- Management is the best and most vivid treasure trove of computer science
  (algorithm, architecture, asynchronous, parallel, distributed and etc.).
  My programming approach is a fusion of “functional programming” and “enterprise management”. 
  It has an added benefit，It is very helpful for the communication 
  between the information technology department and the management of the company.
  - The **Gantt chart** in management, with the timeline as the main axis, multiple data flows running in parallel. 
    It is also a good data flow , parallel and asynchronous programming tool. 
  - Parallel: Large industrial pipelines can be expanded linearly in parallel.
  - Distributed: group company model, total branch model, parent-subsidiary model, holding company model, etc.
  - Architecture (Organizational Structure): 
    - Pyramid: Classic top-down modular design
    - Flat: The Pure Function Pipeline Data Flow
    - Matrix: AOP (Aspect-Oriented Programming)
    - Business Process Outsourcing (BPO): Functional programming (FP), Not only does FP use its own department 
      functions to complete tasks, but there are also non-working departments such as higher-order functions, 
      but it can delegate tasks to outsourced company functions as parameters.
            
      ```clojure
      (own-department-function  task-list)

      (apply outsourced-company-function task-list)
      ```

    - Chaos P2P (peer to peer): OOP (Object-Oriented Programming)
    
  - OS or Resource Management: ERP
  - Type system: Industrial standard system (ISO standard, national standard, industry standard, enterprise standard), 
    The Pure Function Pipeline Data Flow is suitable for the implementation of product specification (data specification) 
    industry standard system, Just like the computer hardware interface standard specification, Hardware is a function, 
    interface is a data standard specification. Data is transferred between hardware (functions).
  - Storage Management(buffers, caches, databases, etc.): Inventory management
  - In a large business, `process first thinking` is based on the credential(evidence). This credential is data.
    Credentials (data) flow through the nodes in the process to form a dataflow.
    Every node in the process is a pipe-function (pure function).
    This is the `Pure Function Pipeline Data Flow`.
  - Boeing aircraft pulse production line technology, just like confluence technology of rivers from the source to the sea. 
    It's also a variant of the **Gantt Chart**.
       
![Gantt chart](./doc/image/GanttChart.jpeg) 

![River](./doc/image/river.jpeg) 

### Warehouse Workshop Model

Warehouse(database, pool)/Workshop(pipeline) Model is simple and practical model, 
and the large industrial assembly line is the mainstream production technology in the world.

![Warehouse Workshop Model](./doc/Warehouse-Workshop-Model.svg)

Everything is unified:
  
#### The unification of programming technology and system architecture

  ```
  Programs = Algorithm + Data Structures
        ---- Niklaus Wirth, Turing Award (1984), Father of Pascal
  
  It’s better to have 100 functions operate on one data structure 
  than 10 functions on 10 data structures.        
        ---- Alan Perlis
             the first recipient of the Turing Award (1966)
             A founding father of Computer Science as a separate discipline
        
  ```
  - Warehouse: Data Structure, database. Obviously, the database should be as global and unique as possible.
  
  - Workshop: Algorithms, Functions.
  
  - Extended case
    - UI architecture: All components interact around an atom state, 
      which is better than each component managing its own state.
      - Warehouse: An atom state that includes all UI components, similar to "a data structure" of Alan Perlis.
      - Workshop: individual components, similar to "100 functions" of Alan Perlis.
      
#### The unification of single-threaded and multi-threaded and asynchronous and distributed
  
  "Fire-and-Forget" is a guidance bullet with independent guidance capability. It does not need external support, 
  it will automatically track and strike the target, and do not need to control after launching. 
  The utility model has the advantages of improving the use efficiency between the missile and the launcher, 
  and reducing the missile's dependence on other systems to provide its own updated information, 
  so that the launcher can attack the largest number of targets in the shortest time and improve the survival of the launcher. 
  The development direction of guidance technology in the future is precisely the "Fire-and-Forget" precision guidance technology.
  
  For the same reason, I think the development direction of concurrent and parallel programming technology is also "Fire-and-Forget", 
  so asynchronous is unnecessary, async / await is a backward and inevitably eliminated model. Change from focusing on 
  "code and function development" to "data control, data flow management, data lifecycle management, data standardization system, 
  process improvement (process reengineering), thread collaborative optimization, etc."
  
  The abolition of async / await is essentially "operations research". When waiting, this thread should be over. 
  For example, in a factory, it will not happen that one workshop stops in the middle of the production process 
  and waits for its products at the door of another workshop to continue the production process.
  Each workshop only interacts with the warehouse. After the main thread (also the workshop) sends out order data, 
  The production plan is generated by the warehouse, and data (messages) are sent to the relevant workshops for production 
  until the task is completed. There is no waiting in the entire process, but the production plan is generated according to the order, 
  with the warehouse as the center, and each workshop independently produces in parallel.
  
  Existing pragmatic "Fire-and-Forget" concurrency and parallel technology: software transactional memory (STM), multi-versioned concurrency control (MVCC), git.  
  
  - Product: Standardized data
  
  - Warehouse: data management, sending & receiving, like: 
    Enterprise Warehouse（DB） + [MES (Manufacturing Execution System)](https://wikimili.com/en/Manufacturing_execution_system) = Database + DBMS.
    - MES can be used as a workshop, but it is integrated with the warehouse as a DBMS. 
      The combination of the DBMS and the database is more reasonable and the performance is higher.
    - Notify thread production data by order (production plan) or inventory level
    - Send the data to the thread, if the thread does not exist, create a thread.
    - Fire-and-Forget: Forget after sending the data.
    
  - Workshop: Intelligent-thread,  Super-Microservice
    - Because `Fire-and-Forget`, the thread should have autonomy and intelligence, so it is called `Intelligent-thread`.
    - Except for input / output data. It is isolated from the outside world, Forget after the data is sent to the warehouse.
    - Passive production: Lean Production, JIT (Just In Time) Production, production by order (production plan), 
      pursuit of zero inventory and quick response.
    - Active lazy production: When the data of the warehouse (cache) is lower than the minimum inventory level, 
      the thread starts to produce data to fill the warehouse until the optimal inventory level is reached.
      
  - Distributed case: [`Flink Stateful Functions`](https://ci.apache.org/projects/flink/flink-statefun-docs-release-2.0/concepts/distributed_architecture.html)
  
    - `Flink Stateful Functions` is more like my previous article: 
      [Everything is RMDB](https://github.com/linpengcheng/PurefunctionPipelineDataflow/blob/master/doc/Everything_is_RMDB.md).

    - In the `Warehouse/Workshop Model`, There is strictly no interaction between each workshop, 
      and the "Flink Stateful Functions" diagram seems to be interactive. From this perspective, 
      `Flink Stateful Functions` only defines the warehouse, and does not strictly define the workshop. 
      It is a state server, which is more similar to Clojure ring web server. 
      [Clojure Web Application Model: Ring is also a `Warehouse/Workshop Model`](#Clojure-Web-Application-Model)

    - In the `Warehouse/Workshop Model`, the vast majority of the workshop is a pipeline-function (pure function) or 
      long-pipeline-function consisting of serially connected pipes. If not, then the side effect is at the end of 
      the series of pipeline, similar to that, consumables such as office supplies or lubricants are sent 
      from the warehouse to the workshop and are consumed.
      
    - `Flink Stateful Functions` did not emphasize  two-way "Fire-and-Forget".

    - Finally, I think it would be better if `Flink Stateful Functions` adhered to the `Warehouse/Workshop Model` more strictly.
    
    - [Related discussions on clojureverse](https://clojureverse.org/t/fire-and-forget-the-unification-of-single-threaded-multi-threaded-and-asynchronous-programming-technology/6032)

##### The unification of Microservice and Intelligent-thread

Reference: [The unification of `single-threaded`, `multi-threaded`, `asynchronous` and `distributed`](#The-unification-of-single-threaded-and-multi-threaded-and-asynchronous-and-distributed), Every Intelligent-thread is a microservice.

  - Product: Standardized data
  
  - Warehouse: data management

  - Workshop: Intelligent-thread,  Microservice

#### The unification with Information System Integration Model

  Many large enterprises have independent information systems produced 
  by different manufacturers and need to integrate and integrate.
  
  - Workshop: individual subsystems  
  - Warehouse: The information system integration layer acts as a individual system.
    - A unified abstraction layer (virtual database, virtual data warehouse) 
      of enterprise global data, external data requests of any subsystem 
      are requested from this system without knowing from that subsystem.
      Achieve the unity of the global system.
    - The middle layer and data routing of system interconnection.
    - Isolation change: When one subsystem changes, 
      it does not cause chain changes of other subsystems.
    - Putting the complexity in one place is better than 
      putting it in all places.
    - The subsystems are guaranteed independence, 
      and the simplicity of each subsystem is realized.
    - Ultimately, the simplicity and unity of the global system is achieved.
  - Extended case
    - The OS provides a large number of APIs for developers to use, 
      but if the OS API changes may be more frequent, 
      such as adding methods or variables, but previously developed 
      applications are likely to use the old OS API. 
      Solution: Develop directly a version compatibility layer independently, 
      and freely develop the OS.
      - Warehouse: individual version compatibility layer
      - Workshop: OS, applications, programming languages, etc.
    - Intel's CPU starts with Pentium, the kernel switches to RISC, 
      and the external CISC compatibility layer
    - Microsoft's Windows 95/NT system also supports DOS and Windows 3.1 
      applications with a separate compatibility layer.
    - Apple's Mac OS X uses an independent compatibility layer to 
      support legacy applications of System OS.
    - The integration layer is similar to an e-commerce platform (warehouse), 
      and a shop (subsystem) is similar to a workshop, but each shop (or consumer) only 
      needs to conduct purchase and sales activities and exchanges through the e-commerce 
      platform.There is no need for direct communication or Pay attention to the details of 
      how the product (data) is formed or circulated.
    
#### The unification with Microkernel Architecture
  
  - Warehouse: Core system
  - Workshop:  Plug-in modules
  - Product: Message, Data
  
### The unification with AOP

Similar to in an industrial zone, there is a global professional sewage treatment plant, 
the input sewage is treated separately.

- Warehouse
   - Two types of data (products) are stored
     - External input data
     - Middleware: Pipeline functions conforming to 
       data interface standards
   - Data queue
     - Single queue or hash-map (classified queue)
     - Receive external data.
   - Middleware queue
     - Single queue or hash-map (classified queue)
     - Support dynamic modification
     - Storage middleware

- Workshop
   - Receive external data
   - Apply middleware to process data
     - Single queue: According to the metadata carried by 
       the input data.
     - hash-map (classified queue): According to 
       the key (type) of the input data and  the middleware.
   - Based on the metadata carried in the output data, 
     call the external receiving pipeline function.
     
### The unification with Event-driven Architecture

- Warehouse
   - The database has a trigger mechanism
   - Clojure has `add-watch`
- Workshop
   - Database trigger
   - Clojure's `watch` function     
  
#### The unification with Computer Hardware Architecture
  
  The model uses memory as the core, not the CPU.
  
  - Warehouse: Memory
  - Workshop: CPU, graphics card, sound card, etc.
  - Standardized data: data transmitted between hardware 
    that conforms to industry standard interfaces
  - Acceptance: Motherboard with standardized interfaces
    such as PCI, SATA, USB, etc.
  - External standardized data: hard disk, flash drive, etc.
  
#### The unification with Integrated Circuit System

  - Warehouse: Battery
  - Workshop: Integrated Circuit Components (Chip, Board), Electrical Equipment
  - Standardized data: current
  - External standardized data: power plants
  
#### The unification with Programming Language Platform

  Like julia, a lisp is built into the internal core or internal representation, 
  and the popular grammar is used externally. Therefore, 
  the grammar is not a problem at all, and the compiler and the grammar 
  can evolve independently, go hand in hand, freely, efficiently, and flexibly. 
  You can do several languages at the same time, 
  such as Julia native support for julia and lisp syntax, 
  and third-party implementation of clojure syntax, 
  performance equivalent to native grammar. 
  Converting clojure grammar to lisp grammar is simpler than 
  native julia grammar conversion. Implementing a language on a platform 
  represented by a lisp is very simple, such as Racket. 
  Regardless of which one of the developers likes swift, python, ruby, 
  scala, f`#` and java, all of them are implemented separately, 
  all of them are satisfied.
  
  - Warehouse 
    - Standard library
    - Library represented by internal lisp that 
      compiled from Code written in various external languages.
    - Native compiler compiled libraries and applications
    
  - Workshop
    - Native compiler: Get the lisp intermediate code from the repository, 
      compile and output to the repository.
    - Internal lisp core: Write code in lisp, 
      or compile output intermediate code in other languages.
    - External languages: Compile and output each language code to the warehouse. 
      Each language does not have to interact with each other. 
      Just interact with the repository. Multi-language on the racket language 
      is the mechanism.

#### The unification with Clojure Web Application Model

  - product standard (data interface): the req-map and resp-map of the ring 
  
  - warehouse: the ring
  
  - workshop: the functions on both sides of the C/S, 
    and Raw materials (hash-map) are transferred to each other 
    through warehouses through interactions.

  Therefore, I recommend functions with single hash-map type parameters.
  This parameter can be mapped to standards, datatable, 
  database (with constraints, stored procedures, schemas, etc.) as needed.
  The Clojure’s immutable persistent data structure does not cause data cloning, 
  which is suitable for this scene.
  
#### The unification with Lifecycle Management

  Algorithms derived from Chinese myths that have been circulating for thousands of years: 
  The book of life and death in hell.
    
  - Product (data): Soul (component)

  - Warehouse (Database): The book of life and death in hell,
    which saves all matters and status of all living things 
    (components) from life to death, and can trigger triggers 
    to monitor events, and can change the status and lifetime 
    of living things (components) according to events.

  - Workshop
  
    - Judge: At the end of the component's life, the judge 
      function rewards good and punishes evil according to 
      the book of life and death.

    - Old Lady Mengpo: Restore the soul (component) to its 
      original state.

    - The six great divisions in the wheel of karma: 
      Resource Pool

    - Hell: punishment, destruction, garbage collection

#### The unification with Other Models 

  - Warehouse
    - Standardized data model
    - RMDB
  - Workshop
    - The Pure Function Pipeline Data Flow
    - Industrial production line
    - Watertight compartment of the vessel
  - Standardized data
    - Industry standard products
      - Raw material
      - Semi finished product
      - Finished product
  - Acceptance
    - Quality control department
    - customs
  - External standardized data
    - Purchasing department
    - supply chain
 
#### Warehouse Workshop Model Summary
 
In industry, the product standard is the interface, 
the production method (code implementation) is not limited, 
input the raw materials (data) that conform to the standard, 
and output the products (data) that conform to the standard,
that’s all.

Before entering the warehouse, 
all data must be acceptance-checked first.

The input and output of the workshop 
can only be standardized data, 
the input-data comes from the warehouse, 
the output-data is acceptance-checked 
and sent to the warehouse.

Therefore, there will be no 
abnormal/error/Illegal data inside the workshop, 
no need to check data.

The code in the workshop is a pure function pipeline data flow,
it's simple, reliable, high-performance, 
Easy to debug, easy to observe, easy to maintain, easy to expand.

The workshop and the workshop are independent, 
non-interactive, Like a Lego module or 
a ship's watertight compartment, 
internal changes or abnormalities 
in any one workshop do not affect other workshops.  

## The difference between it and others

### Disadvantages of FP and OO

FP and OO are overly complicated, and it is not feasible in large industries. It is also a kind of production method that emphasizes personal technology in hand workshops. Personal technology greatly affects product quality and extremely unreliable production methods.FP and OO are actually taking a detour, highly embellished and ineffectual, and produce all kinds of fail.

Excessive application of OO and FP design patterns, in addition to increasing complexity 
and error probability, reduce performance, without any benefit. 
Complex networks of relationships between objects in the OO system are also difficult to maintain..

I tend to construct systems with the simplest concepts and the most basic techniques, syntax, and functions. Used to implement my mind, The Pure Function Pipeline Data Flow is the simplest, stable, reliable and readable.. There is a great poet Bai Juyi in China. even illiteracy understands and appreciates his poetry. I hope that my code can be understood by the junior programmer even in the most complicated system.

### The difference between it and middleware

The code looks similar, but the idea is essentially different.

- The input and output of the middleware's function is a function, 
the flow is a layer-packed function. It like concentric circles, 
and middleware debugging is very troublesome.

- The input and output of the PureFunctionPipelineDataflow's function is data, flow is data, 
it is linear series and parallel. It like a line.

I can't agree with the idea of middleware,
It is in conflict with the idea of integrated circuits. 
In the circuit, the component (board) cannot be circulated, 
only the data (current) can flow, which is the essential difference.

### The difference between it and Rx

It is essentially different between it and [Rx](http://reactivex.io/):

- The essential difference between programming methods is the inherent thoughts and models. The idea and model of pure function pipeline data flow are highly consistent with integrated circuits.

- `The Pure Function Pipeline Data Flow` emphasizes the data flow, Rx emphasizes the asynchronous event flow, and does not mention or emphasize the data flow.

- `The Pure Function Pipeline Data Flow` is composed of only 5 components, and the model is much simpler than Rx.

- `The Pure Function Pipeline Data Flow` emphasizes the sequential structure (series of pipeline components), and the maintenance (code reading, expansion, debugging, etc.) is simpler.

- The asynchronous event flow of `The Pure Function Pipeline Data Flow` is simpler than Rx. I wrote an asynchronous event flow in my project, it is just a queue processing, It's too simple, so there's no need to mention it specifically.

- The Clojure language doesn't require [RxClojure](https://github.com/ReactiveX/RxClojure) at all.

### The difference between it and traditional unix-like pipe operator in FP language

- Traditional unix-like pipe operator in FP language

  - Just simply  simulate a water pipe.
  
  - Just as a tip in a code snippet.

- Pure function pipeline data flow

  - Systematic simulation of integrated circuit systems and large industrial production lines.
  
  - Covers all aspects of system architecture, data modeling, data manipulation, data application, etc.
  
## Basic quality control

Basic quality control of pure function pipeline data flow. The code must meet the following three basic quality requirements before you can talk about other things. These simple and reliable evaluation criteria are enough to eliminate most unqualified codes.
- **Function evaluation:** Just look at the shape of the code (pipeline structure weight), and whether the function is a pure function.
- **Dataflow evaluation:** A data flow has at most one side effect and can only be placed at the end.
- **System evaluation:** Just look at the circuit diagram, you can treat the function as a black box like an electronic component.
- **Code Quality Visualization:** 
  - For Lisp languages, S expression is contour graph, 
    can be very simple transformation into contour map, or 3D mountain map.
  - If the height of the mountains is not high, and the altitude value is similar,
    it means that the quality of the code is good.
  - For non-Lisp languages, you can convert the source code into an abstract syntax tree (AST), 
    and then into a contour map, or a 3D mountain map.
    
## Tao

According to Taoism, water flow is the perfect substance. The water flow is always able to assume any shape as needed, sequential processing, until the mission is completed, reaching the end. The pure function pipeline data flow is like a water flow, almost the Tao.

Clojure just adds four persistent collections and some core functions to the JVM, and expresses the code with four persistent collections. It has no syntax, It can change as needed, like water flow, almost the Tao.

Tao is simplicity, Tao is the law of nature, Tao is algorithm, Tao is everything in everywhere on everytime.
Tao is the great unification of everything.
Therefore, Integrated Circuit Technology, Industrial Assembly Line Production Technology, 
Accounting, Management, Architecture etc. 
everything can be used as Algorithms and Software Engineering Methods.
They can transform each other.

![TaoTaiji](./doc/TaoTaiji.gif)

## Killer Application

### Software Design and Develop Automation

Software Design and Develop Automation (SDDA)

```
Software and hardware design is less different than software designers think, 
but more different than hardware designers think.
        ---- Fred Brooks, Turing Award (1999), The Mythical Man-Month
```

Because the `pure function pipeline data flow` realizes the unity of software and hardware 
on the logical model, it solves the problem of Fred Brooks.

- `The Pure Function Pipeline Data Flow` is the basics and the only way to SDDA.
- SDDA is an innovative and revolutionary approach to develop large-scale software.
- `The Pure Function Pipeline Data Flow` systematically simulates an integrated circuit system, 
  so SDDA can be implemented like electronic design automation (EDA).  
- Establishing a simple, unified, standardized, and systematic pipeline component library is the basis 
  of SDDA, which is also the only correct method for SDDA. It's like EDA.
- AI selects components from the pipeline component library 
  according to the data standard specification of initial state and final state. 
  AI combines pipeline components that meet the data standard specification to complete the task. 
  If there is no corresponding pipeline component, It can be automatically generated by AI 
  (or developed manually by developers).

## Great Historical Significance

```
Fools ignore complexity. Pragmatists suffer it. Some can avoid it. Geniuses remove it.
      ---- Alan Perlis, Epigrams in Programming.
           the first recipient of the Turing Award (1966)
           A founding father of Computer Science as a separate discipline           

When the solution is simple, God is answering.
Everything should be as simple as possible, but not simpler.
Most of the fundamental ideas of science are essentially simple, and may, as a rule, 
be expressed in a language comprehensible to everyone.
If you can't explain it simply, you don't understand it well enough.
Any intelligent fool can make things bigger, more complex, and more violent. 
It takes a touch of genius -- and a lot of courage -- to move in the opposite direction. 
      ---- Albert Einstein
           The greatest folk scientist in history :-)
           A professional clerk in the patent office 
           An amateur physicist
           Nobel prize in Physics (1921)
           
Make folk sciences great again :-)
1. Perfectly defeat other messy and complex software engineering methodologies 
   in a simple and unified way.
2. Realize the unification of software and hardware on the logical model.
   and the unification of programming technology and system architecture 
   through the innovative Warehouse Workshop Model.
3. Achieve a leap in software production theory 
   from the era of manual workshops 
   to the era of standardized production in large industries.
4. The basics and the only way to `Software Design and Develop Automation (SDDA)`, 
   SDDA is an innovative and revolutionary approach to develop large-scale software,
   just like `Electronic Design Automation (EDA)`.   
5. It is a particular outstanding and trend-setting technical achievement, 
   It fits perfectly with the principal claim to the "Turing Award".
6. I think it should win the "Turing Award", the highest award in the computer field.
7. If I cannot win the Turing Award, it must be that ACM lacks the ability to appreciate technology.
8. History will prove what I said.2020-03-07
      ---- Lin Pengcheng, Self-taught folk scientist
```

The idea of simplicity and unity is an important guiding ideology of scientific research.
Unification of theories is the long-standing goal of the natural sciences; 
and modern physics offers a spectacular paradigm of its achievement. 
It can be found from the knowledge of various disciplines: 
the more universally applicable a unified theory, the simpler it is, 
and the more basic it is, the greater it is.

The Pure Function Pipeline Data Flow, 
based on the philosophy of Taoism and the Great Unification Theory, 
In the computer field, for the first time, 
it was realized that the unification of hardware engineering and software engineering on the logical model.
It has been extended from `Lisp language-level code and data unification` 
to `system engineering-level software and hardware unification`. 
Whether it is the appearance of the code or the runtime mechanism, 
it is highly consistent with the integrated circuit system. 
It has also been widely unified with other disciplines 
(such as management, large industrial assembly lines, water conservancy projects, power engineering, etc.). 
It's also very simple and clear, and the support for concurrency, parallelism, 
and distribution is simple and natural.

There are only five basic components:

1. Pipeline (pure function)

2. Branch

3. Reflow (feedback, whirlpool, recursion)

4. Shunt (concurrent, parallel)

5. Confluence.

The whole system consists of five basic components. 
It perfectly achieves unity and simplicity.
It must be the ultimate programming methodology.

In addition, the IT industry is still a very young and immature discipline.
The current software engineering is still at the level of manual workshops. 
`Pure function pipeline data flow` brings large industrial production theory 
and methods to software engineering. It incorporates IT industry into 
modern large industrial production systems, This is an epoch-making innovative 
theory and method. 

The modern society is an information society, IT controls everything, 
penetrates everything. In my opinion, the development of IT is exactly 
the same as the development of modern large-scale industrial production 
systems. With the development of the IT industry, 
With the development of the IT industry, data standard systems will be 
widely established, improved and interconnected at the international, 
national, industrial and enterprise levels, It will be precisely standardized 
to every smallest part. `pure function pipeline data flow` will become 
the basic theory and Methods have become increasingly important, 
and have become the ultimate standard method for entering textbooks and industry.

The key to the industrialization of the IT industry is to 
establish a complete standard system like the traditional industry. 
software is the pipeline for producing products (data), 
which is no different from traditional industrial production lines.
Therefore, the software production method will adopt enterprise management ideas, 
develop software into something similar to a traditional industrial assembly line, 
input standardized raw materials (data), output standardized products (data), 
and store them in a warehouse (database).

From the perspective of large industrial production theory, 
standardizing the input raw materials (data) and output products (data) 
has the following advantages:

- Fairness, Neutrality: Use data standard specifications to make it fair to different manufacturers, products, 
  algorithms and implementations, and to achieve healthy competition.
- Fungibility: As long as the pipeline (or product) meets the data standard specifications, it can be directly replaced.
- Standard, Data standards (data interfaces, data specifications) are better than code interfaces.
- Interconnectedness: As long as the pipes comply with the data standard specifications, they can be interconnected.
- Combination: Data combination is better than code (function) combination.
- Simplicity: Simplicity is the shortest path to a solution. -- Ward Cunningham (Wiki inventor)
- Unity: 
  - Everything is a pipeline.
  - Realize the unification with the theories of disciplines such as integrated circuits, 
    business management, hydraulics and electricity, and you can refer to their theories.
  - Realize the unification of programming technology and system architecture through innovative warehouse/shop model.
- Predictability: Because the system architecture, design guidelines, and components are simple and unified, 
  it is very clear, so the future development and changes of the system are predictable.
- Reliability: Simplicity is prerequisite for reliability. -- Edsger W. Dijkstra (Turing Award in 1972)
- Easy to expand: Just insert or replace pipes.
- Easy to parallel, Can linearly scale up production (performance).
- Observable, easy to debug, and verifiable. The data flowing between the pipes is very easy to observe, 
  and it is easy to see where the pipes are leaking.
- Clarity: non-IT practitioners can understand.

The role of the standard system can be seen from 
the great progress of social productivity after 
the traditional industry has entered the era of 
large industrial production from the era of manual workshops.

This method has been applied to 100,000 lines of code-level pure clojure project, 
which can prove the practicability of this method.

Finally, If you agree with me, please help me nominate the "Turing Award".

[HOW TO NOMINATE](https://amturing.acm.org/call_for_nominations.cfm)

## Postscript

### Imagination

```
Imagination is more important than knowledge.
The true sign of intelligence is not knowledge but imagination.
Logic will get you from A to B, imagination will take you everywhere.
        ---- Albert Einstein
```

Similar to The most valuable chapter of “Code Complete”----Chapter 2 Metaphors for a Richer Understanding of Software Development, I tend to inspire readers to discover useful patterns from life, work and personal interests, which are then used as solutions for development, rather than to apply mechanically other people’s cases.

In this way there will be endless cases, there will always be endless ways to solve the problem. This is "Tao ".

### Ever-changing

I was asked why I didn't create several pipeline dataflow design patterns like OO and FP, and I thought, in Chinese Classic Myth Fiction "The Journey to the West", Bodhi  asked the monkey if he wanted to learn `Tiangang 36 change patterns` or `Disha 72 change patterns`, when the monkey chose `Disha 72 change patterns`, his failure has become a foregone conclusion, Bodhi who is the Taoist great God must understand the nature of the Tao, learn the laws of nature, The ever-changing truth, whether the monkey chose `Tiangang 36 change patterns`, or `Disha 72 change patterns`, his thoughts from then on stifled shackles, put on the invisible Tight curse, from then on can not approach  "Tao ", this is not teaching, but playing monkey. :-)

### Simplicity

Simplicity does not mean easy.
The pure pipeline system is a simple system. but simplicity does not mean easy. 
Implementing a pure pipeline system is a systematic engineering. 
Hard work must be done to build a complex system into a simple and smooth pure pipeline system. 
This requires great wisdom and pays a lot of difficult Business Process Design (or Reengineering).

```
Many people think that sages have secret tricks, despise simple technology, and pursue complex, 
difficult, and sophisticated technology, but this idea is completely contrary to the facts.
        ---- Wang Yangming, 
             the most famous and well-known thinker, philosopher, calligrapher, 
             strategist, and educator in China
```

In addition, As long as you have carefully read the "pure function pipeline data flow", 
you will find that I only use the most basic common sense to solve the problem, 
and did not use any too complicated and delicate technology.
`Common sense` is `human best practice` or `the most widely used and reliable theory`.

### Principles-based are better than rules-based.

- Principles should be few and important, and should not affect flexibility and creativity. 
  For example: Do not exceed 10 principles.

- Principles should be simple and clear, and enforcement must be ensured.

- Too many rules are equal to no rules, complicated and difficult to enforce, 
  increasing the risk of loopholes, affecting flexibility and creativity.
  
- Implementing software engineering is like leading soldiers to fight.
  Too many and too complicated military disciplines will make it difficult 
  for soldiers to understand and execute, and the final result will be very poor.

- Simplicity and unity must be parallel. Pure piping systems meet this principle.

- Principles should be consistent with mainstream principles of modern industrial production.

### End message

I spend my spare time developing a financial analysis expert system. 
Although my writing time is very limited, but I will gradually improve it. 
compared to the content when I first set up the blog, 
it has been rich and improved a lot.

## Other Articles Table of Contents

### English + Chinese

- [Markdown Literary Programming that don't break the syntax of any programming language](doc/markdown_literary_programming.md)

- [~~Introduction： Basic Methods (Old version)~~](doc/IntroductionBasicMethods.md)

- [Annotation is an unnecessary technique](doc/annotation_is_unnecessary.md)

- [Clojure is a FP based on RMDB. ](doc/Clojure_is_FP_based_on_RMDB.md)

- [Everything is RMDB.](doc/Everything_is_RMDB.md)

- [Mathathematical and AI](doc/math-xiaoyao.md)

- [Why Clojure (Lisp) almost the Tao? ](doc/why_clojure_almost_the_Tao.md)

- [The Best Practice of Clojure.](doc/best_practice_of_clojure.md)

- [Interaction of Static and Dynamic (Taiji and Table Tennis) ](doc/interaction_of_static_and_dynamic.md)

- [Rust: Mathematics can't save programming, but finance can do. ](doc/rust.md)

- [React is terrible.](doc/react_is_terrible.md)

### Chinese

- [Simplicity and Unity ---- Grand Unified Theory, Lisp(Clojure) and Pure Function Pipeline Dataflow](doc/Simplicity_and_Unity.md)

- [The unification of disciplines (Tao, AI, Mathematics, Accounting, Pure function pipeline data flow, International disease classification, etc.)](doc/Tao_ICD.md)

- [Reach the three immortality of `Worth, Work, Words`](doc/immortal.md)

- [Differences in FP, OO and Dataflow](doc/fp_oo_dataflow.md)

- [`Pure Function Pipeline Data Flow` and Jin Yong's "Dragon of the Eight Divisions" Xiaoyao School Martial Arts System](doc/dataflow_xiaoyao.md)

- [Office as a database service](doc/office_is_db.md)

- [The data model is global and strategic, while the function implementation is only local and tactical.](doc/datamodel-vs-function.md)

- [The hero sees the same: Rob Pike，Linus Torvalds，Alan Perlis and me](doc/RobPike-LinusTorvalds-AlanPerlis-Me.md)

- [Pure function pipeline data flow and Integrated circuit](doc/DataflowIC.md)

- [On the pure function pipeline data flow method, and a comment: the left ear mouse "What is functional programming?"](doc/AboutDataflow.md)

- [Functional programming learning: mode method or infinite method?](doc/infinite.md)

- [Code is also a first-class citizen](doc/code-is-first-class.md)

- ["Code Complete" and "Imagination Programming"](doc/CodeComplete.md)

- ["The Art of Computer Programming" and Imagination Programming](doc/TAOCP.md)

- [Myth programming: life and death book, reincarnation, hell](doc/LifecycleManagement.md)

- [Semi-automatic rifle, batch processing and Lazy](doc/lazy.md)

- [Rulai God Palm and tree-seq](doc/tree-seq-and-The-Hand-of-God.md)

- [C# is so stupid that I can't understand it, and on the UI construction method](doc/c%23stupid.md)

- [Google Flutter is a similar technology I created and eliminated](doc/Flutter_is_outdated.md)

- [Example: abstraction, polymorphism, metamorphosis](doc/Ii-abstract.md)

- [Technical architecture of my project: AI, accounting, statistics, legal, anti-virus software](doc/TechnicalFramework.md)

