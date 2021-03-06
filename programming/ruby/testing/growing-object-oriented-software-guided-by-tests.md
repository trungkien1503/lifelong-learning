Meszaros07
# Growing Object-Oriented Software, Guided by Tests

# 1: What is the Point of TDD?

Developers often don't completely understand the technologies they’re using. They have to learn how the components work whilst completing the project.

We think that the best approach a team can take is to use empirical feedback to learn about the system and its use, and then apply that learning back to the system. A team needs repeated cycles of activity. In each cycle it adds new features and gets feedback about the quantity and quality of the work already done. The team members split the work into time boxes, within which they analyze, design, implement, and deploy as many features as they can.

The sooner we can get feedback about any aspect of the project, the better. Many teams in large organizations can release every few weeks. Some teams release every few days, or even hours, which gives them an order of magnitude increase in opportunities to receive and respond to feedback from real users.

Start with an acceptance test, then while it's failing, let it demonstrate that the system does not implement the feature, and when it passes, we're done.

A system is deployable when the acceptance tests all pass, because they should give us enough confidence that everything works.

External quality: how well the system meets the needs of its customers and users.

Internal quality: how well it meets the needs of the developers and administrators.

# 2: Test-Driven Development with Objects

*The big idea is "messaging": The key in making great and growable systems is much more to design how its modules communicate rather than what their internal properties and behaviors should be. -- Alan Kay*

Objects communicate by messages: It receives messages from other objects and reacts by sending messages to other objects and returning a value or exception to the original sender.

Values: Immutable instances that model fixed quantities. They have no individual identity (2 value instances are the same, if they have the same state).

Objects: Use mutable state to model their behavior over time. States can diverge if they receive different messages in the future.

Split the system into values (treated functionally) and objects (implement stateful behavior of the system).

### Follow the Messages

In Java, we identify roles with interfaces rather than concrete classes. In our view, the domain model is in the communication patterns. They show the communication of the different classes.

Ex: In a video game: actors, scenery, effects, GameEngine, renderer, animator, collision detector

## But Sometimes Ask

We ask when getting information from values and collections, or when using a factory to create new objects. Don't do this:

    class Train
      def reserve_seats(reservation_request)
        carriages.each do |carriage|
          if (carriage.get_seats.get_percent_reserved < percent_reserved_barrier)

You are exposing the internal structure of Carriage to implement this. Just answer the questions being asked:

    carriages.each do |carriage|
      if carriage.has_seats_available_with(percent_reserved_barrier)

Adding a query method moves the behavior to the most appropriate object, gives it an explanatory name, and makes it easier to test.

## Unit-Testing the Collaborating Objects

The essential structure of a test:

- Create any required mock objects.
- Create any real objects, including the target object.
- Specify how you expect the mock objects to be called by the target object.
- Call the triggering methods on the target object.
- Assert that any resulting values are valid and that all the expected calls have been made.

# The Process of Test-Driven Development
====================

# 4: Kick-Starting the Test-Driven Cycle

The quandary in writing and passing the first acceptance test is that it's hard to build both the tooling and the feature it's testing at the same time. Changes in one disrupt progress made with the other, and tracking down failures is tricky when the architecture, the tests, and the production code are all moving.

To solve this, we split it into two smaller problems: First, work out how to build, deploy, and test a "walking skeleton", then use that infrastructure to write the acceptance tests for the first meaningful feature.

Walking skeleton: "thinnest possible slice of real functionality that we can automatically build, deploy, and test end-to-end". Ex, for a database-backed web application, a skeleton would show a flat web page with fields from the database.

We want our test to start from scratch, build a deployable system, deploy it into a production-like environment, and then run the tests through the deployed system. We want to make sure btw that the scripts have been exercised by the time we have to deploy for real.

*Nothing forces us to understand a process better than trying to automate it.* This is also often the moment where the development team bumps into the rest of the organization and has to learn how it operates.

## Deciding the Shape of the Walking Skeleton

We can't automate the build, deploy, and test cycle without some idea of the overall structure. Rule of thumb: We should be able to draw the design for the "walking skeleton" in a few minutes on a whiteboard.

We use the automation of building and testing to give us feedback on qualities of the system, such as how easily we can cut a version and deploy, how well the design works, and how good the code is.

## Expose Uncertainty Early

It usually takes time to get a "walking skeleton" working -- the first step involves establishing a lot of infrastructure and asking many questions. (This is unpredictable.) After a few features have been implemented and the project automation has been built up, a routine is made. The end-game should be a steady production of functionality. The mundane and brittle tasks, such as deployment and upgrades, will have been automated so that they "just work".

For an existing system, the safest way to start the TDD process is to automate the build and deploy process, and then add end-to-end tests that cover the areas of the code we need to change. Then we can start to address internal quality issues with more confidence, refactoring the code and introducing unit tests as we add functionality.

# 5: Maintaining the Test-Driven Cycle

## Start Each Feature with an Acceptance Test

*We use terminology from the application's domain (not from the underlying technologies), so we aren't tied up about the implementation of the test.* This also shields our acceptance test suite from changes to the system's technical infrastructure. If a third-party organization changes the protocol used by their services from FTP and binary files to web services/XML, we should not have to rework the tests for the systems' application logic.

When we write acceptance tests to describe a new feature, we expect them to fail until that feature has been implemented; new acceptance tests describe work yet to be done. Once passing, the acceptance tests now represent completed features and should not fail again. A failure means that there's been a regression and that we've broken our existing code.

*Unit and integration tests support the development team, should run quickly, and should always pass, although they might take longer to run. New acceptance tests represent work in progress and will not pass until a feature is ready.*

## Start testing with the Simplest Success Case

Do the simplest case that could possibly work (simple should not be interpreted as simplistic). Degenerate cases don't add much to the value of the system anyway. Focusing on the failure cases at the beginning of a feature is bad for morale -- if we only work on error handling it feels like we're not achieving anything.

*Start with the simplest success case, so we'll have a better idea fo the real structure of the solution, and we can prioritize handling failures or other success cases.*

Example of incremental approach: moon program. The 7 missions had their own steps: (unmanned command module test, unmanned lunar module test, manned CSM in low Earth orbit, manned CSM and LM in low Earth orbit...

Write the test that you'd want to read--we want each test to be as clear as possible an expression of the behavior to be performed by the system or object. Ignore the fact that the test won't run or compile, just concentrate on its text; we act as if the supporting code already exists.

*Test must read well.*When the test reads well, we then build up the infrastructure to support the test. We know we've implemented enough of the supporting code when the test fails in the way we'd expect.

*Watch the test fail.* If the test fails in a way that we didn't expect, we know we've misunderstood something or that the code is incomplete, so we fix that. If it's the "right" failure, we can check that the diagnostics are helpful.

*Develop for the Inputs to the Outputs.* The end-to-end tests for the feature will simulate these events arriving. At the boundaries of our system, we will need to write one or more objects to handle these events. Then we discover that these objects need services, and these need objects, and these need services...

In this way, we work our way through the system: from the objects that receive external events, through the intermediate layers, to the central domain model, and then on to other boundary objects that generate an external visible response.

It's tempting to start by unit-testing new domain model objects and then trying to hook them into the rest of the application, but we're more likely to get bitten by integration problems later.

*Unit-test behavior, not models. A test called `test_bid_accepted` tells us what it does, not what it's for.* We do better when we focus on the features that the object under test should provide. We need to know how to use the class to achieve a goal, not how to exercise all the paths through its code.

*Listen to the tests.* If code is difficult to test, the most likely cause is that our design needs improving.

# 6: Object-Oriented Style

Value code that is easy to maintain over code that is easy to write. Implementing a feature in the most direct way can damage the maintainability of the system, by making the code difficult to understand or by introducing hidden dependencies between components.

We grow our systems a slice of functionality at a time. As the code scales up, the only way we can continue to understand and maintain it is by structuring the functionality into objects, objects into packages, packages into programs, and programs into systems.

## 2 Principal Heuristics:

*Separation of concerns.* When we have to change the behavior of a system, we want to change as little code as possible. If all the relevant changes are in one area of code, we don't have to hunt around the system to get the job done.

Because we can't predict when we have to change any part of the system, we gather together code that will change for the same reason. Ex: code to unpack messages from an Internet standard protocol will not change for the same reasons as business code that interprets those messages, so we partition the two concepts into different packages.

*Higher levels of abstraction.* The only way for humans to deal with complexity is to avoid it, by working at higher levels of abstraction.

"Ports and adapters" -- code for business domain is isolated from its dependencies on technical infrastructure, such as databases and user interfaces. We don't want technical concepts to leak into the application model, so we write interfaces to describe its relationships with the outside world *its terminology.* Then we write bridges between the application core and each technical domain (adapters).

*Encapsulation.* Ensures that the behavior of an object can only be affected through its API. (Private/public thing).

*Information hiding.* Conceals how an object implements its functionality behind the abstraction of its API. It lets us work with higher abstractions by ignoring lower-level details that are unrelated to the task at hand.

While working with badly encapsulated code, we spend too much time tracing where the potential effects of a change might be, looking at where objects are created, what common data they hold, and where their contents are referenced.

We follow standard practices to maintain encapsulation when coding: define immutable value types, avoid global variables and singletons, copy collections and mutable values when passing them between objects, and so on.

## Internals vs. Peers

As we organize our system, we must decide what is inside and outside each object, so that the object provides a coherent abstraction with a clear API. Much of the point of an object is to encapsulate access to its internals through its API and hide these details from the rest of the system.

## Helpers/Functional Programming

We often write helper methods to make code more readable. We're not afraid of adding very small methods if they clarify the meaning of the feature they represent. We use the *message-passing* style we've described between objects, but we tend to use a more functional style within an object, building up behavior from methods and values that have no side effects.

Features without side effects mean we can assemble our code from smaller components, minimizing the amount of risky shared state.

*Every object should have a single, clearly defined responsibility, this is the SRP. When we're adding behavior to a system, this principle helps us decide whether to extend an existing object or create a new service for an object to call.*

Our heuristic is that we should be able to describe what an object does without using "and" and "or". If we find ourselves adding clauses to the description, then the object probably should be broken up into collaborating objects usually one for each clause.

This principle also applies when we're combining objects into new abstractions. If we're packaging behavior implemented across several objects into a single construct, we should be able to describe its responsibility clearly.

## Object Peer Stereotypes

*Dependencies.* Services that the object requires from its peers so it can perform its responsibilities. The object cannot function without these services. *You should not be able to create an object without them.*

*Notifications.* Peers that need to be kept up to date with the object's activity. The object will notify interested peers whenever it changes state or performs a significant action.

Notifications are "fire and forget"; the object neither knows nor cares which peers are listening. Notifications are so useful because they decouple objects from each other. Ex: In a UI, a button component promises to notify any registered listeners when it's clicked, but does not know what those listeners will do. Similarly, the listeners expect to be called but know nothing of the way the UI dispatches its events.

*Adjustments.* Peers that adjust the objects' behavior to the wider needs of the system. Ex: policy objects that make decisions on the object's behalf, and components parts of the object if it's a composite. Ex: A `JTable` will ask a `TableCellRenderer` to draw a cell's value. If we change the renderer, the table will change its presentation.

Dependencies must be passed in through the constructor. *Partially creating an object and then finishing it off by setting properties is brittle because the programmer has to remember to set all the dependencies. (We get `NullPointerException`).*

Notifications and adjustments can be passed to the constructor as a convenience. They can be initialized to safe defaults and overwritten later. We then add methods to allow callers to change these default values, and add or remove listeners.

*Composite Simpler Than the Sum of Its Parts*

All objects in a system, except for primitive types built into the language, are composed of other objects. When composing objects into a new type, we want this new type to exhibit simpler behavior than all of its component parts considered together. The composite object's API must hide the existence of its component parts and the interactions between them, and expose a simpler abstraction to its peers.

Bad:

    money_editor.get_amount_field.set_text(String.value_of(money.amount))
    money_editor.get_currency_field.set_text(money.currency_code)

    money_editor.set_amount_field(money_amount())
    money_editor.set_currency_field(money_currency_code())

Good:

    money_editor.set_value(money)

*The API of a composite object should not be more complicated than that of its components.*

The "context independence" rule helps us decide whether an object hides too much or hides the wrong information. *A system is easier to change if its objects are context-independent: if each object has no built-in knowledge about the system in which it executes.* We can take units of behavior (objects) and apply them in new situations.

*To be context-independent, whatever an object needs to now about the larger environment it's running must be passed in.* Those relationships might be "permanent" (passed in on construction) or "transient" (passed in to the method that needs them).

*In this "paternalistic" approach, each object is told just enough to do its job and wrapped up in an abstraction that matches its boundary.*

*Eventually, the chain of objects reaches a process boundary, which is where the system will find external details such as host names, ports, and UI events.*

One Domain Vocabulary: Better to make their relationships explicit, defined separately from the objects themselves.

*Hiding the Right Information.* It makes sense to hide the data structure used for the cache in the `CachingAuctionLoader` class. Sometimes it doesn't make sense to hide the name of the application's log file in the `PricingPolicy` class.

# 7: Achieving Object-Oriented Design

A caller wants to know what an object does and what it depends on, but not how it works. We also want an object to represent a coherent unit that makes sense in its larger environment--a system built from such components will have the flexibility to reconfigure and adapt as requirements change.

*Starting with a test means we describe WHAT we have to achieve before we consider HOW.* This focus helps us maintain the right level of abstraction for the target object.

## How Writing a Test First Helps the Design

If the intention of the unit test is unclear, then we're probably mixing up concepts and not ready to start coding.

To keep unit tests understandable, we have to limit their scope--no unit tests more than twelve lines please. The component they're testing is probably too large and needs to be broken up into smaller components.

To construct an object for a unit test, we have to pass its dependencies to it, which means we know what they are. This encourages context independence, since we have to be able to set up the target object's environment before we can unit-test it. Objects with implicit or too many dependencies is painful to prepare for testing.

## Communication over Classification

We want to achieve a well-designed class structure, but we think the communication patterns between objects are more important.

TDD with mock objects also encourages information hiding. We should mock an object's peers--its dependencies notifications, and adjustments, but not its internals. Tests that highlight an object's neighbors help us to see whether they are peers, or should instead be internal to the target object.

A test that is clumsy or unclear might be a hint that we've exposed too much implementation, and that we should rebalance the responsibilities between the object and its neighbors.

## Introducing Value Types

*Values are immutable, and objects have state, so they have identity and relationships with each other.* The more we write, the more we've convinced that we should define types to represent value concepts in the domain, even if they don't do much.

*Breaking out.* Break a class that handles incoming messages into two parts: one to parse the message string, one to interpret the result of the parsing.

*Budding off.* When we want to mark a new domain concept in the code, we often introduce a placeholder type that wraps a single field, or maybe has no fields at all. As the code grows, we fill in more detail in the new type by adding fields and methods.

*Bundling up.* When we notice that a group of values are always used together, we take that as a suggestion that there's a missing construct. Create a new type with fixed public field, then migrate behavior to the new type.

## Where Do Objects Come From?

When code becomes complex to understand, we can pull out cohesive units of functionality into smaller collaborating objects, which we can then unit-test independently. Splitting out a new object also forces us to look at the dependencies of the code we're pulling out.

Concern 1: How long should we wait before doing something? Concern 2: Occasionally it's better to treat this code as a spike--once we know what to do, just roll it back and reimplement cleanly.

When code is more stable and ha some degree of structure, we discover new types by "pulling" them into existence--*we might be adding behavior to an object and find that, following our design principles, some new feature doesn't belong inside it.*

When implementing an object, we discover that it needs a service that needs to be provided by another object. We give the new service a name and mock it out in the client object's unit tests, to clarify the relationship between the two. Then we write an object to provide that service and discover what services that object needs. Then, follow this chain until we connect up to existing objects, either our own or from a third-party API.

### Bundling Up: Hiding Related Objects into a Containing Object

When we have a cluster of related objects that work together, we can package them up in a containing object. The new object hides the complexity in an abstraction that allows us to program at a higher level.

Nice effects: We have to give it a name which helps us understand the domain a little better. Second, we can scope dependencies more clearly, since we can see the boundaries of the concept. Third, we can be more precise with our unit testing--we can test the new composite object directly, and use a mock implementation to simplify the tests for code from which it was extracted.

*When the test for an object becomes too complicated to set up, considering bundling up some of the collaborating objects.*

### Identify Relationships with Interfaces

We use interfaces to name the roles that objects can play and to describe the messages they'll accept. We also prefer interfaces to be as narrow as possible, even though that means we need more of them. *The fewer methods there are on an interface, the more obvious is its role in the calling object.*

Narrow interfaces are also easier to write adapters and decorators for; there's less to implement, so it's easier to write objects that compose together well.

What's good about driving an interface from its client is that it avoids leaking excess information about its implementers, which minimizes implicit coupling between objects.

### Refactor Interfaces Too

In a reasonably large code base, we often start to find interfaces that look similar. This means we should look at whether they represent the same concept and should be merged. Extracting common roles makes the design more malleable because more components will be "plug-compatible", so we can work at a higher level of abstraction.

Consider refactoring interfaces when we start implementing them. If we find that the structure of an implementing class is unclear, perhaps it has too many responsibilities.

Organize the code into two layers: implementation (the graph of objects), and declarative (builds up the objects in the implementation layer using small "sugar" methods. *The declarative layer describes what the code will do, while the implementation layer describes how the code does it.*

On classes, we view it as an "implementation detail"--a way of implementing types, not the types themselves.

# 8: Building on Third-Party Code

We don't control third-party code, so we cannot use our process to guide its design. Instead, we must focus on the integration between our design and the external code.

In integration, we have an abstraction to implement. With the third-party API pushing back at our design, we must find the best balance between elegance and practical use of someone else's ideas.

## Only Mock Types That You Own

When we use third-party code we often do not have a deep understanding of how it works. This means that providing mock implementations of third-party types is of limited use when unit-testing the objects that call them.

We also have to make sure that the behavior we stub or mock matches what the external library will actually do.

## Write an Adapter Layer

If we don't want to mock an external API, how can we test the code that drives it? We write a layer of adapter objects that uses the third-party API to implement these interfaces. We keep this layer as thin as possible, but wrap it enough so that technical implementations aren't leaked into the application domain model.

Exceptions for where mocking is useful: simulating behavior that is hard to trigger with the real library, such as throwing exceptions. We might use mocks to test a sequence of calls to make sure that a transaction is rolled back if there's a failure (there shouldn't be many tests like this in a test suite).

Adapter objects are passive (reacting to calls from our code.) Sometimes, adapter objects must call back to objects from the application. Ex, event-based libraries usually expect the client to provide a callback to be notified when an event happens.

In these cases, we do use mock objects when testing objects that integrate with third-party code, but only to mock the callback interfaces defined in the application.

# A Worked Example
====================

# 9: Commissioning an Auction Sniper

Auction Sniper is an application that watches online auctions and automatically bids slightly higher whenever the price changes, until it reaches a stop-price of the auction closes.

Terms:

- Current price: Current highest bid for the item.
- Stop price: The most a bidder is prepared to pay for an item.
- XMPP (Jabber) used for the underlying communication layer. It streams XML elements across the network. There is no central server, and a user can log in to an XMPP server simultaneously from multiple devices or clients (known as resources).

## The Auction Protocol

- A bidder joins an auction. The sender of the XMPP message identifies the bidder and the name of the chat session identifies the item.
- Auctions send events, which can be:
    - Price: An auction reports the currently accepted price/minimum increment that the next bid must be raised by.
    - Close: Auction is closed.

So we want to figure out a series of incremental development steps for the Sniper app. The first is the absolutely smallest feature we can build. Skeleton = Swing, XMPP, and the app, to show that we can plug these components together.

### Sequence:

- Single item: join, lose without bidding
- Single item: join, bid, lose. Add bidding to connectivity.
- Single item: join, bid, win. Distinguish to see who bid the winning bid.
- Show price details. UI.
- Multiple item support.
- Add items through the UI.
- Stop bidding at the stop price: More intelligence in the Sniper algorithm.

# 10: The Walking Skeleton

**Iteration Zero.** This is the first stage where the team is doing initial analysis, setting up its physical and technical environments, and getting started. The team isn't adding much visible functionality since almost all the work is infrastructure.

We call this *iteration zero*: we still need to timebox the activities but we don't have functional dev yet (we start in iteration 1).

We start by writing a test as if its implementation already exists, and then filling in whatever is needed to make it work ("programming by wishful thinking"). Working backwards from the test helps us focus on what we want the system to do instead of getting caught up in the complexity of how we will make it work. Then we can build the infrastructure on how we want the test to pass.

We want our skeleton test to exercise our application as close to end-to-end as possible, to show that the `main()` method initializes the application instead of directly invoking its domain objects. Hide UI code/Swing in `ApplicationRunner` class.

    public class AuctionSniperEndToEndTest {
      private final FakeAuctionServer auction = new FakeAuctionServer("item-54321");
      private final ApplicationRunner application = new ApplicationRunner();

      @Test public void sniperJoinsAuctionUntilAuctionCloses() throws Exception {
        auction.startSellingItem();
        application.startBiddingIn(auction);
        auction.hasReceivedJoinRequestFromSniper();
        auction.announceClosed();
        application.showsSniperHasLostAuction();
      }
    }

In writing the test, one of the assumptions we've made is that a `FakeAuctionServer` is tied to a given item.

*One Domain at a Time.* We use auctions and Snipers, nothing about messaging layers or components in the UI. Keeping the language consistent helps us understand what's significant in this test.

We need to write:

- XMPP message broker. Openfire.
- Stub auction that can communicate over XMPP. WindowLicker.
- GUI testing framework
- Test harness that can cope with multithreaded, asynchronous architecture
- Automated build/deploy/test process

## End-to-End Testing

Tests run in parallel with the application and do not know precisely when the application is or isn't ready, unlike unit testing where a test drives an object directly in the same thread and so can make direct assertions about its state and behavior.

The technique is to poll for the effect and fail if it doesn't happen within a given time limit. This means that end-to-end testing is slower and more brittle, so failures might need interpretation. *Sometimes timing-related tests have to fail several times in a row before they're reported. This is unlike unit tests which must all pass every time.*

The first test is not really E2E, in that it doesn't include the real auction service, because it is not easily available. We want to set the boundaries of what to test and how to eventually cover everything. In this case, we have to start with a fake auction service. The documentation might or might not be correct, so we will record that as a known risk in the project plan and schedule time to test against the real server as soon as we have enough functionality to complete a meaningful transaction.

# 11: Passing the First Test

## Building the Test Rig

- Start up Openfire server.
- Create accounts for the Sniper and the auction.
- Run the tests.
- Each test starts instances of the app/fake auction and then test communication through the server.

## `ApplicationRunner`

This is an object that wraps up all management and communicating with the Swing app we're building. It runs the application from the command line, obtaining and holding a reference to its main window or querying the state of the GUI and for shutting down the app at the end of the test.

WindowLicker will control Swing, synchronize Swing threads, and manipulate features in the Swing UI.

    class ApplicationRunner {
      public static final String SNIPER_ID = "sniper";
      public static final String SNIPER_PASSWORD = "sniper";
      private AuctionSniperDriver driver;

      public void startBiddingIn(final fakeAuctionServer auction) {
        Thread thread = new Thread("Test Application") {
          @Override public void run() {

> We call the application through its `main()` function to make sure we've assembled the pieces correctly. Convention is entry point to an app would be the `Main` class in the top-level package.

            try {

> Bid for one item only.

              Main.main(XMPP_HOSTNAME, SNIPER_ID, SNIPER_PASSWORD, auction.getItemId());
            } catch(Exception e) {
              e.printStackTrace();
            }
          }
        };

        thread.setDaemon(true);
        thread.start();

> Turn down the timeout period for finding frames and components. The default values are longer than we need for a simple app like this one and will slow down the tests when they fail.

        driver = newAuctionSniperDriver(1000);
        driver.showsSniperStatus(STATUS_JOINING);

> Expect a `LOST` status, or else get an exception.

      public void showsSniperHasLostAuction() {
        driver.showSniperStatus(STATUS_LOST);
      }
    }
  }

## Fake Auction

A `FakeAuctionServer` is a substitute server that allows the test that allows the test to check how the Auction Sniper interacts with an auction using XMPP messages.

Responsibilities:

- Connect to the XMPP broker and accept a request to join the chat from the Sniper
- Receive chat messages from the Sniper or fail if no message arrives within some timeout
- Must allow the test to send messages back to the Sniper
- Smack, the XMPP client, is event-driven, so the fake auction has to register listener objects for it to call back. Two types of events: events about a chat, such as people joining, and events within a chat, such as messages being received.

## Fake Auction Server Code

    public class FakeAuctionServer {
      public static final String ITEM_ID_AS_LOGIN = "auction-%s";
      public static final String AUCTION_RESOURCE = "Auction";
      public static final String XMPP_HOSTNAME = "localhost";
      private static final String AUCTION_PASSWORD = "auction";

      private final String itemId;
      private final XMPPConnection connection;
      private Chat currentChat;

      public FakeAuctionServer(String itemId) {
        this.itemId = itemId;
        this.connection = new XMPPConnection(XMPP_HOSTNAME);
      }

      public void startSellingItem() throws XMPPException {
        connection.connect();
        connection.login(String.format(ITEM_ID_AS_LOGIN, itemId), AUCTION_PASSWORD,
            AUCTION_RESOURCE);
        connection.getChatManager().addChatListener(new ChatManagerListener() {
          public void chatCreated(Chat chat, boolean createdLocally) {
            currentChat = chat;
            chat.addMessageListener(messageListener);
          }
        });
      }

This fake is a minimal implementation just to support testing. We use a single instance variable to hold the chat object, but in production there should be several chats for all the bidders.

[TODO]: This_part

## The Necessary Minimum

Skeleton: The point is to design and validate the initial structure of the end-to-end system--where end-to-end includes deployment to a working environment--to prove that our choices of packages, libraries, and tooling will actually work.

# 12: Getting Ready to Bid

The core behavior of a Sniper is that it makes a higher bid on an item in an auction where there's a change in price..

Each acceptance test we should write should have just enough new requirements to force a manageable increase in functionality, so we decide that the next one will add some price information.

Sniper will have to distinguish between Price and Close events from the auction, display the current price, and generate a new bid. New test:

    public class AuctionSniperEndToEndTest {
      @Test public void
        sniperMakesAHigherBidButLoses() throws Exception {
          auction.startsSellingItem();

> Wait for the stub auction to receive the Join request before continuing with the test.

          application.startBiddingIn(auction);
          auction.hasReceivedJoinRequestFromSniper();

> Tell the stub auction to send a message back to the Sniper with the news that at the moment the price of the bid is 1000, the increment for the next bid is 98, and the winning bidder is the other bidder.

          auction.reportPrice(1000, 98, "other bidder");

> Ask the `ApplicationRunner` to heck that the Sniper shows that it's now bidding after it's received the price update message from the auction.

          application.hasShownSniperIsBiddng();

> Ask the stubbed auction to check that it has received a bid from the Sniper that is equal to the last price plus the minimum increment.

          auction.hasReceivedBid(1098, ApplicationRunner.SNIPER_XMPP_ID);

          auction.announceClosed();
          ApplicationRunner.showsSniperHasLostAuction();
      }
    }

*We use integers to represent value. In a real system, we would deine a domain type to represent monetary values, using a fixed decimal implementation.*

*How can we hope to catch all the configuration options in an entire system?* At some level we can't, and this is why we have professional testers. We can push to exercise as much as possible of the system as possible, and do it repeatedly.

*Use `null` When An Argument Doesn't Matter.* `UNUSED_CHAT` is a meaningful name for a constant that is defined as `null`. For now we use this because a real `Chat` object is difficult to instantiate. If we don't need one for the current functionality, we just pass in null to satisfy the compiler, but use a named constant to make clear its significance.

This `null` is not a `null object` which may be called and do nothing in response. This `null` is just a placeholder and will fail if called during the test.

## Finish the Job

We write a high-level E2E test to describe what Sniper should implement; we write long unit test names to tell us what a class does; we extract new classes to tease apart fine-grained aspects of the functionality, and we write lots of little methods to keep each layer of code at a consistent level of abstraction.

# 13: The Sniper Makes a Bid

Better to fiddle about naming methods `sniperLost()` vs. `auctionClosed()` because the teams that spend too little time clarifying the code pay for it in maintenance overhead. SRP is important, and devs shouldn't be shy about creating new types.

Introduced another collaborator: `Auction`. `Auction` is about financial transactions, it accepts bids for items in the market, and `SniperListener` is about feedback to the application. It reports changes to the current state of the Sniper.

When writing the test, we realize that we don't care if the Sniper notifies the listener more than once that it's biding; it's just a status update, so we can use an `atLeast(1)` clause for the listener's expectation. *We do care that we send a bid exactly once, so we use a one() clause for its expectation.*

*Null Implementation.* It's similar to a null object, but the intention is different. A null object is usually one implementation amongst, many, introduced to reduce complexity in the code that calls the protocol.*A null implementation is a temporarily empty implementation, introduced to allow the programmer to make progress by deferring effort and intended to be replace.*

*Encapsulate Collections.* Our rule of thumb is that we try to limit passing around types with generics. Particularly when applied to collections, we view it as a form of duplication. It's a hint that there's a domain concept that should be extracted into a type.

## The Sniper Wins the Auction

*Representing Object State.* Sometimes, we want to make assertions about an object's behavior depending on its state, but we don't want to break encapsulation by exposing how that state is implemented.

# 15: Towards a Real User Interface

When we use public final fields, it makes obvious that this value is immutable and reduces the overhead of maintaining getters when the class isn't yet stable.

Nothing shakes out a design like trying to implement it, and very few people are smart enough to get their designs always right.

## Observations

`SnipersTableModel` has one responsibility: to represent the state of our bidding in the user interface. We've seen too much user interface code that is brittle because it has business logic mixed in.

Adding little slice of behavior: Replace a label with a table, get that working; show the Sniper building ,get that working; add the other values, get that working.

Celebrate changing your mind. We renamed several features in the code, and we learned more about what the structure should be by using the code we've written, and we learn more about the names we've chosen when we work with them.

# 16: Sniping for Multiple Items

We don't need to be able to combine items using the UI yet. We start with a test. Then, we prepare for multiple items.

It took us a couple of attempts to get this design pointing in the right direction because we were trying to allocate behavior to the wrong objects. For each attempt to write tests that made sense, the setup and our assertions kept drifting apart.

# 17: Teasing Apart Main

We've noticed that we've interleaved different domain levels, auction sniping, and chatting, in this one unit of code. The object that locks this code into Smack is the chat.

*Compromising on a Constructor.* Our experience is that busy constructors enforce assumptions that one day we will want to break, especially when testing, so we prefer to keep them very simple--just setting the fields.

*Incremental Architecture.* The restructuring of Main is a key moment--we know have a structure that matches the "ports and adapters" architecture.

- Core domain code (Auction Sniper)
- Bridging code (SnipersTableModel)
- Technical code (JTable)

Tech Domain    | Adapters           | App Domain       | Adapters    | Tech Domain
-----------------------------------------------------------------------------------
Smack Library  | XMPP Auction House | Sniper Launcher  | Table Model | JTable
               | XMPP Auction       | Sniper Portfolio |             |
               |                    | Auction Sniper   |             |

By repeatedly fixing local problems in the code, we find we can explore the design safely, never straying for more tan a few minutes from working code. Usually this is enought to lead us towards better design.

*Three-point Contact.* Trained climbers only move one limb at a time to minimize the risk of falling off. Each move is minimal and safe, but combining enough of them will get you to the top of the route.

# 18: Filling in the Details

# 19: Handling Failure

In practice, reporting a messages failure means that we flush the price and bid values, and show the status as Failed for the offending item. We also record the event somewhere so that we can deal with it later.

The issue in this design is not the complexity of the feature, which is constant, but how we divide it up. We emphasize SRP. Our experience is that focused responsibilities made the code more maintainable because we don't have to cut through unrelated functionality to get to the piece we need.

We mock the Logger class because we care about the rendering of the values into a failure message with a severity level. The class is very limited, so we don't think it's worth introducing another level of indirection to define the logging role. We don't think it's worth running against a new file since that introduces dependencies that aren't really relevant to the functionality we're developing.

*Inverse Salami Development.* For each new feature, write some tests that show what it should do, work through each of those tests changing just enough code to make it pass, restructure the code as needed either to open up space for new functionality or to reveal new concepts, then ship it.

The skill is in learning how to divide requirements up into incremental slices, always have something working, and always adding just one more feature. To make this work, we must understand how to change the code incrementally, and critically, keep the code well structured so that we can take it wherever we need to go.

*Small Methods to Express Intent.* Our aim is to do what we can to make each level of code as readable and self-explanatory as possible, repeating the process all the way down until we actually have to use a Java construct.

*Logging Is Also a Feature.* We've seen many systems where logging has been added ad hoc by developers wherever they find a need. Production logging is an external interface that should be driven by the requirements of those who will depend on it, not by the structure of the current implementation.

# Sustainable Test-Driven Development
=====================================

We want to make sure the tests pull their weight by making them expressive, so that they can tell what's important when we read them and when they fail, and we make sure they don't become a maintenance drag themselves.

Difficulty in testing might imply that we need to change our test code, but it's often a hint that our design ideas are wrong and that we ought to change the production code.

# 20: Listening to the Tests

The trick is to let our tests drive our design. TDD is about testing code, verifying its externally visible qualities such as functionality and appearance. TDD is also about feedback on the code's internal qualities: the coupling and cohesion of its classes, dependencies that are explicit or hidden, and effective information hiding--the qualities that keep the code maintainable.

When we find a feature that is difficult to test, we don't just ask ourselves how to test it, but also why it is difficult to test.

## I Need to Mock an Object I Can't Replace: Singletons Are Dependencies

Singleton = making objects accessible through a global structure. Any code that needs access to a feature can just refer to it by its global name instead of receiving it as an argument.

    Date now = new Date();

Under the hood, the constructor calls the singleton `System` and sets the new instance to the current time using `System.currentTimeInMilliseconds()`. The cost of this is that we have to make the test wait overnight our we modify the internal clock somehow to return suitable Date values for the test. We have to introduce a `Clock` object and pass it into the `Receiver`.

In this case, better to put the date checking part in `Clock` rather than `Receiver`.

## Logging is a Feature

Two separate features in logging:

- Support logging (errors and info) is part of the UI of the application. These messages are intended to be tracked by support staff to diagnose a failure or monitor the progress of the running system.
- Diagnostic logging (debug and trace) is infrastructure for programmers. These messages should not be turned on in production because they're intended to help the programmers understand what's going on inside the system they're developing.

Support logging should be test-driven from somebody's requirements, such as auditing or failure recovery. These tests make sure we've thought about what each message is for and made sure it works.

Diagnostic logging is driven by the programmers' need for fine grained tracking of what's happening in the system. (Don't use this in production!)

*Notification Rather Than Logging.* Writing tests against static global objects, including loggers, is clumsy. We have to either read from the file system or manage an extra appender object for testing; we have to remember to clean up afterwards so the tests don't interfere with each other, and set the right level on the right logger.

Oftentimes code will do 2 things, the functional part and the logging part (breaks SRP). What we want:

    Location location = tracker.getCurrentLocation();
    for (Filter filter : filters) {
      filter.selectFor(location);
      support.notifyFiltering(tracker, location, filter);}

The support object might be implemented by a logger, a message bus, pop-up windows, or whatever is appropriate; this detail is not relevant to the code at this level.

Code is easier to test, because we (not the logging framework) own the support object, so we can pass in a mock. Now, we're testing for objects, rather than formatted contents of a string.

*The idea of encapsulating support sounds like it's over-design, but it means we're writing code in terms of our *intent* (helping the support people) rather than implementation (logging), so i's more expressive.* Support reporting is handled in a few known places, so it's easier to be consistent about how things are reported and to encourage reuse.

The act of writing a test for each report avoids the "I don't know what to do with this exception, so I'll log it and carry on" syndrome, which leads to log bloat and production failures because we haven't handled obscure error conditions.

## Mocking Concrete Classes

One approach to interaction testing is to mock concrete classes rather than interfaces. The technique is to inherit from the class you want to mock and override the methods that will be called within the test, either manually or with the mocking frameworks.

Just do this when you have no other options. The problem is that when you subclass, there's nothing in the domain code to make such a relationship visible--it's just methods on an object.

We can do this when we're either working with legacy code or when we're working with third-party code that we can't change at all. It's almost always better to write a veneer over an external library than mock it directly.

Do not override a class' internal features--this just locks down your test to the quirks of the current implementation. Only override visible methods. This rule also prohibits exposing internal methods just to override them in a test. If you can't get to the structure you need, then the tests are telling you that it's time to break up the class into smaller, composable features.

## Don't Mock Values

Don't do this:

    Video video1 = context.mock(Video.class);

Just create instances with the appropriate times and use them. Values are immutable. If you're tempted to mock a value because it's too complicated to set up an instance, consider writing a Builder.

## Bloated Constructor

Sometimes, we end up with a constructor that has a lot of arguments. We most likely got there by adding the object's dependencies one at a time, and it got out of hand. We can pack some of the arguments together and replace them with a new object to represent it.

Not so good:

    public MessageProcessor(MessageUnpacker unpacker, AuditTrail auditor, CounterPartyFinder counterPartyFinder, LocationFinder locationFinder, DomesticNotifier domesticNotifier, ImportedNotifier importedNotifier);

Better:

    public MessageProcessor(MessageUnpacker unpacker, AuditTrail auditor, MessageDispatcher dispatcher);

When two things are always used together, put them together!

Being sensitive to complexity in the tests can help us clarify our designs. We now have a message handling object that performs the usual three stages: receive, process, and forwards, but we were able to pull out the message routing code so the `MessageProcessor` has fewer responsibilities and we know where to put routing decisions when things get more complicated. It's also easier to unit test.

When extracting implicit components, we start by looking for two conditions: arguments that are always used together in the class, and those that have the same lifetime.

## Confused Object

The object itself might be too large because it has too many responsibilities. The smell for this class is that its test suite will look confused too. The tests for its various features will have no relationship with each other, so we'll be able to make major changes in one area without touching others.

If we can break up the test class into slices that don't share anything, it might be best to go ahead and slice up the object too.

## Too Many Dependencies

Not all of the arguments might be dependencies. *We insist on dependencies being passed in to the constructor, but notifications and adjustments can be set to defaults and reconfigured later. When a constructor is too large, and we don't believe there's an implicit new type amongst the arguments, we can use more default values and only overwrite them for particular test cases.*

> Bad:

    public class RacingCar {
      private final Track track;
      private Tires tires;
      private Suspension suspension;
      private Wing frontWing;
      private Wing backWing;
      private double fuelLoad;
      private DrivingStrategy driver;

      public RacingCar(Track track, DrivingStrategy driver...){
        this.track = track;
        this.driver = driver;
        ...
      }
    }

As seen in the `final` thingie only the `Track` is the dependency. What we should do is this:

    public class RacingCar {
      private final Track track;
      private DrivingStrategy driver = DriverTypes.borderlineAggressiveDriving();
      private Tires tires = TireTypes.mediumSlicks();
      private Suspension suspension = SuspensionTypes.mediumStiffness();
      private Wing frontWing = WingTypes.mediumDownforce();
      private Wing backWing = WingTypes.mediumDownforce();
      private double fuelLoad = 0.5;

      public RacingCar(Track track){
        this.track = track;
      }

      public void setSuspension(Suspension suspension) { ... }
      public void setTires(Tires tires) { ... }
    }

## Too Many Expectations

When everything is an expectation, everything looks equally important. We can't tell what's significant and what's there to get through the test. We can make our intentions clearer by distinguishing between stubs (simulations of real behavior) and expectations (assertions we want to make about how an object interacts with its neighbors).

## What the Tests Will Tell Us

*Keep knowledge local.* Some of the test smells we've identified, such as needing "magic" to create mocks, are to do with knowledge leaking between components. If we can keep knowledge local to an object (either internal or passed in), then its implementation is independent of its context; we can safely move it wherever we like.

*If it's explicit, we can name it.* One reason why we don't like mocking concrete classes is that we like to have names for the relationships between objects as well as the objects themselves. If we have a name, we can control it.

*More names mean more domain information.* When we emphasize how objects communicate, we end up with types and roles defined more in terms of domain than of implementation. When we have a greater number of smaller abstractions, we get further away from the underlying language.

*Pass behavior rather than data.* By applying "Tell, Don't Ask" repeatedly, we end up with a coding style where we tend to pass behavior into the system instead of pulling values up through the stack. More precise interfaces give us better information-hiding and clearer abstractions.

It's much easier to keep a codebase clean than to recover from a mess. Once a codebase starts to rot, the developers will be under pressure to botch the code to get the next job done.

Unit tests shouldn't be 1000 lines long, it should focus on at most a few classes and should not need to create a large fixture or perform lots of preparation just to get the objects into a state where the target feature can be exercised. TDD can be unforgiving.

# 21: Test Readability

Teams that adopt TDD either see a boost in productivity because tests let them add features with confidence, or the pace slows them down and the tests themselves become a burden. Test code should describe what production code does. *It's concrete about the values it uses as examples of what results to expect, but abstract about how the code works.* Production code is abstract about the values but concrete about how it gets the job done. When writing production code, we consider how we compose our objects to make up a working system. It's more important for test code to express the intention of its target code than to plug into a web of other objects.

## Watch Out For:

- Test names that do not clearly describe the point of each test case and its differences from the other test cases.
- Single test cases that seem to be exercising multiple features.
- Tests with different structure, so the reader cannot skim-read them to understand their intention.
- Tests with lots of code to set up and handle exceptions (buries essential logic).
- Tests that use literal values but are not clear about what is significant about those values.

*Test Names Describe Features.* Don't name tests `test1()`. We don't need to know that `TargetObject` has a `choose()` method--we need to know what the object does is different situations, and what the method is for.

Better to name tests in terms of the features that the target object provides (`holdsItemsInTheOrderTheyWereAdded()`). These names can be as long as we like because they're only called through reflection and we never have to type them in to call them.

The point of the convention is to encourage the developer to think in terms of what the target object does, not what it is.

As a matter of style, the test name should same something about expected result, the action on the object, and the motivation for the scenario. Better to have `notifiesListenersThatServerIsUnavailableWhenCannotConnectToItsMonitoringPort` rather than `pollsTheServerMonitoringPort`.

*Regularly Read Documentation Generated From Tests.* Make it an effort to see the problems if we're too close to the code to see.

## Canonical Test Structure

If it is difficult to write a test in a standard form, that's our hint that the code is too complicated or that we haven't clarified our ideas.

1. *Setup.* Prepare the context of the test, the environment in which the target code will run.
2. *Execute.* Call the target code, triggering the tested behavior.
3. *Verify.* Check for a visible effect that we expect form the behavior.
4. *Teardown.* Clean up leftover states. Make it an effort to see the problems if we're too close to the code to see.

All code should emphasize WHAT it does over HOW, including test code; the more implementation detail is included in a test method, the harder it is for the reader to understand what's important. We try to move everything out of the test method that doesn't contribute to the description, in domain terms, of the feature being exercised.

We also extract common features into methods that can be shared between tests for setting up values, tearing down state, making assertions, and occasionally triggering the event. The only caution with factoring out test structure is that we have to be careful not to make a test so abstract that we cannot see what it does anymore

*Accentuate the positive. We only catch exceptions in a test if we want to assert something about them.*

*Delegate to subordinate objects.* Sometimes helper methods aren't enough and we need helper objects to support the tests. We developed `ApplicationRunner`, `AuctionSniperDriver`, and `FakeAuctionServer` so we could write tests in terms of auctions and Snipers, not in terms of Swing and messaging.

Two approaches: Either we write the test we want to see and fill in the supporting objects, or to write the code in the tests and refactor out any clusters of behavior.

*Assertions and Expectations.* The assertions and expectations of a test should communicate `precisely` what matters in the behavior of the target code. We regularly see code where tests assert too much detail, which makes them difficult to read and brittle when things change. BTW, it is encouraged to use matchers as opposed to things like `assertFalse()`.

*Literals and Variables.* Literal values without explanation can be difficult to understand because the programmers has to interpret whether a particular value is significant or is just an arbitrary placeholder. Better to have `public static final Chat UNUSED_CHAT = null` to show that we use `null` to represent an argument unused in production code.

# 22: Constructing Complex Test Data

Testing objects with complicated constructors is hard (in production we don't initialize always, but in tests we initialize for every test). The *object mother* pattern is an attempt to break this problem. This makes tests more readable by packaging up the code that creates new object structures and giving it a name. Another pattern is the builder pattern.

    public class OrderBuilder {

      public static OrderBuilder anOrder() {
        return new OrderBuilder();
      }

      public OrderBuilder withCustomer(Customer customer) {
        this.custoner = customer;
        return this;
      }

      public OrderBuilder withOrderLines(OrderLines lines) {
        this.lines = lines;
        return this;
      }

      public OrderBuilder withDiscount(BigDecimal discountRate){
        this.discountRate = discountRate;
        return this;
      }

      public Order build() {
        Order order = new Order(customer);
        for(OrderLine line: lines) order.addLine(line);
          order.setDiscountRate(discountRate);
        }
      }
    }

In here, if you just need an Order object, you can do this: `Order order = new OrderBuilder().build();` but if you need particular values within an object you can specify just those values and use defaults for the rest.

    new OrderBuilder().withOrderLines(lines).withDiscount(new BigDecimal(5.00)).build()

- They keep tests expressive and resilient to change.
- They wrap up most of the syntax noise when creating new objects.
- They make the default case simple, and special cases not much more complicated.
- They protect the test against changes in the structure of its objects: If we ad an argument to a constructor, then all we have to change is the relevant builder and those tests that drove the need for the new argument.
- Easier to spot errors. Compare:

    TestAddresses.newAddress("14th Avenue", "Cubao")
    newAddressBuilder().withStreet("14th Avenue").withCity("Cubao")

[TODO]: TEST_BUILDERS.

# 23: Test Diagnostics

*The point of a test is not to pass but to fail. We want the production code to pass its tests, but we also want the tests to detect and report any errors that do exist. A "failing" test has actually succeeded at the job it was designed to do.*

The situation we want to avoid is when we can't diagnose a test failure that has happened. The last thing we should have to do is crack open the debugger and step through the tested code to find the point of disagreement. We might end up in "debug hell", with deadlines to meet but no idea of how long a fix will take.

# 24: Test Flexibility

We also want to make sure that each test fails only when its relevant code is broken. Otherwise, we end up with brittle tests that slow down development and inhibit refactoring. Causes of brittleness:

- Tests too tightly coupled to unrelated parts of the system or unrelated behavior of the objects they're testing.
- Tests overspecify the expected behavior of the target code, constraining it more than necessary.
- Duplication when multiple tests exercise the same production code behavior.

*If an object is difficult to decouple from its environment because it has many dependencies or its dependencies are hidden, its tests will fail when distant parts of the system change.*

*Test for Information, Not Representation.* Don't use things like `null`, create constants like `NO_CUSTOMER_FOUND` or `UNUSED_CHAT`.

*Precise Assertions.* In a test, focus the assertions on just what's relevant to the scenario being tested. Avoid asserting values that aren't driven by the test inputs, and avoid reasserting behavior that is covered in other tests.

Testing for equality doesn't scale well as the value being returned becomes more complex. Same with strings, you just want to know that the critical information is included.

*Precise Expectations.* Each mock object test should specify just the relevant details of the interactions between the object under test and its neighbors. The combined unit tests for an object describe its protocol for communicating with the rest of the system.

Allowances: We use them as stubs to feed values into the object, to get the object into the right state for the behavior we want to test. They support the interaction we're testing. We often use them as stubs to feed values into the object, to get the object into the right state for the behavior we want to test.

*Allow Queries; Expect Commands.* Commands are calls that are likely to have side effects, to change the world outside the target object. Queries don't change the world, so they can be called any number of times.

This rule helps to decouple the test from the tested object. If the implementation changes (caching, different algorithm), the test is still valid. (Though if we were writing a test for a cache, we would want to know exactly how the query is made.

*Invocation Order.* Only enforce it when it matters.

# 25: Testing Persistence

# 26: Unit Testing and Threads

# 27: Testing Asynchronous Code

Ex of difficult: Not sure if buying or selling has happened before you test the result (HTTP stuff is being sent).

The difficulty with testing asynchronous code is that a test triggers activity that runs concurrently with the test and therefore cannot immediately check the outcome of the activity. The test will not block until the activity has finished.

This implies that every tested activity must have an observable effect: a test must affect the system so that its observable states becomes different. *2 ways to test: by sampling the observable state or listening for events it sends out.*

Synchronizing by simply making each test wait for a fixed time is not practical. We know we'll have to wait for failing tests to time out, but succeeding tests should be able to finish as soon as there's a response from the code.

[TODO]: THREADING.

# A Brief History of Mock Objects

"I had noticed a tendency to add getter methods to our objects to facilitate testing. This felt wrong, since it could be seen as violating OOP principles."

How can we safely peel back and test layers like an onion without impacting its design? The solution was to focus on the composition of software components. We removed the getters from sections of our code and used a compositional strategy by adding constructors that took the objects we wanted to test via getters as parameters.

More of our conversations were about expecting things to happen between our objects and we frequently had variables with names like `expectedURL` and `expectedServiceName` in our objects. We started adding variables like `actualURL` and `actualServiceName` to allow the injected test objects to throw exceptions with helpful messages.
