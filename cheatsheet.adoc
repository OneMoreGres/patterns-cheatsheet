= Design patterns
:experimental:
:source-highlighter: prettify
:source-language: cpp
:stylesheet: cheatsheet.css
:noheader:
:nofooter:

:creational-type: creation
:structural-type: structure
:behavioral-type: behavior
:concurrency-type: async
:architectural-type: architecture


[cols="6*"]
|===

// Creational

a|
== Abstract factory [pattern-type]#{creational-type}#

create similar hierarchies
```
struct OrcFactory: ArmyFactory {
  Fighter* createFighter () override {
    return new OrcFighter;
  }
  Officer* createOfficer () override {
    return new OrcOfficer;
  }
}
```

a|
== Factory method [pattern-type]#{creational-type}#

create single object
```
struct OrcBlacksmith : Blacksmith {
  Weapon* createWeapon () override {
    return new OrcWeapon;
  }
}
```

a|
== Builder [pattern-type]#{creational-type}#

create object from parts
```
struct OrcBuilder {
  void setWeapon (Weapon weapon) {
    orcWeapon = weapon;
  }
  void setArmor (Armor armor) {
    orcArmor = armor;
  }
  Orc build () {
    return Orc (orcWeapon, orcArmor);
  }
}
```

a|
== Step builder [pattern-type]#{creational-type}#

wizard-like object creation
```
struct OrcBuilder : Armorer, Builder {
  Builder* setArmor (Armor armor) override { // Armorer
    orcArmor = armor;
    return static_cast<Builder*>(this);
  }
  Orc build () override { // Builder
    return Orc (orcWeapon);
  }
}
```

a|
== Lazy initialization [pattern-type]#{creational-type}#

create object only when it is needed
```
struct Castle {
  Castle () : kitchen (nullptr);
private:
  Kitchen& getKitchen () {
    if (!kitchen) {
      kitchen = createKitchen ();
    }
    return *kitchen;
  }
}
```

a|
== Multiton [pattern-type]#{creational-type}#

limit objects variety
```
struct Kingdom {
  static Kingdom& get (Name name) {
    static map<Name,Kingdom> kingdoms;
    return kingdoms[name];
  }
}
```

a|
== Object mother [pattern-type]#{creational-type}#

create preset objects
```
struct OrcObjectMother {
  Orc createAngry () {
    return Orc (State::Angry);
  }
  Orc createWounded () {
    Orc orc;
    orc.setHealth (90);
    return orc;
  }
}
```

a|
== Object pool [pattern-type]#{creational-type}#

store and reuse expensive objects
```
struct Museum {
  Artifact& acquire ();
  void release (Artifact& returned);
private:
  set<Artifact> all;
  set<Artifact*> taken;
} 
```

a|
== Prototype [pattern-type]#{creational-type}#

create objects by copying prototype
```
struct Monster {
  virtual Monster* clone () = 0;
}
struct MonsterFactory {
  Monster* create (Type type) {
    return prototypes[type]->clone ();
  }
private:
  map<Type, Monster*> prototypes;
}
```

a|
== Resource acquisition is initialization (RAII) [pattern-type]#{creational-type}#

make object responsible for its resources
```
struct OrcShaman {
  OrcShaman () {
    ManaSource::addLeacher (this);
  }
  ~OrcShaman () {
    ManaSource::removeLeacher (this);
  }
}
```

a|
== Singleton [pattern-type]#{creational-type}#

allow only one instance
```
struct Earth {
  static Earth& instance () {
    static Earth earth;
    return earth;
  }
  int getRadius () const {
    return radius;
  }
private:
  Earth ();
  int radius;
}
```

a|
== MonoState [pattern-type]#{creational-type}#

many instances with single state
```
struct Earth {
  Earth ();
  int getRadius () const {
    return Earth::radius;
  }
private:
  static int radius;
}
```



// Structural


a|
== Abstract document [pattern-type]#{structural-type}#

dynamically manage properties
```
struct MonsterDocument {
  void set (Type, Value);
  Value get (Type);
}
struct Orc : MonsterDocument, Armed {
  Weapon weapon () override { // Armed
    return Weapon (get (Type::Weapon));
  }
}
```

a|
== Adapter [pattern-type]#{structural-type}#

adapt foreign api
```
struct OrcTypeAdapter : Monster, Orc {
  void attack () override {
    Orc::smash ();
  }
}
struct OrcObjectAdapter : Monster {
  void attack () override {
    orc.smash ();
  }
}
```

a|
== Bridge [pattern-type]#{structural-type}#

separate interface and implementation changes
```
struct Fighter : Soldier {
  Fighter (Creature* impl);
  void attack () override {
    impl->attackImpl ();
  }
}
struct Orc : Creature {
  void attackImpl () override;
}
```

a|
== Composite [pattern-type]#{structural-type}#

treat composite object same way as single
```
struct Kingdom : Area {
  double square () override {
    return sum (owned, Area::square());
  }
  void addArea (Area*) override;
private:
  set<Area*> owned;
}
```

a|
== Decorator [pattern-type]#{structural-type}#

dynamically add/remove behavior to object
```
struct Walled : public Town {
  Walled (Town* decorated);
  int strength () override {
    return decorated->strength () + 10;
  }
}
Town* castle = new Walled (new Town ());
```

a|
== Event aggregator [pattern-type]#{structural-type}#

gather all events in one place
```
struct Aggregator {
  void subscribe (Subscriber*);
  void publish (Event event) {
    each (subscribers,
      Subscriber::handle (event));
  }
}
officer.subscribe (fighter);
officer.publish (AttackEvent ());
```

a|
== Extension object [pattern-type]#{structural-type}#

provide additional interface without inherritance
```
struct Kingdom {
  Kingdom (Capital* capital);
  Capital* getCapital () {
    return capital;
  }
}
```

a|
== Facade [pattern-type]#{structural-type}#

single interface for several subsystems
```
struct Army {
  void attack () {
    officers->makeOrders ();
    fighters->followOrders ();
    shamans->prey ();
  }
}
```

a|
== Flyweight [pattern-type]#{structural-type}#

many similar objects with shared state
```
struct Forge {
  Weapon craft (Type type) {
    return Weapon (stats[type]);
  }
private:
  map<Type, WeaponStats*> stats;
}
```

a|
== Front controller [pattern-type]#{structural-type}#

handle all requests in one place
```
struct Controller {
  void handle (Request request) {
    getProcessor (request.type)
      .process (request);
  }
  Processor getProcessor (RequestType);
}
```

a|
== Marker [pattern-type]#{structural-type}#

indicate class behaviour
```
struct Orc : Agressive {
}
if (dynamic_cast<Agressive*>(monster))
  monster->attack ();
if (dynamic_cast<Defensive*>(monster))
  monster->guard ();
```

a|
== Module [pattern-type]#{structural-type}#

group connected functions
```
struct ConsoleModule {
  void prepare ();
  void unprepare ();
  static ConsoleModule& instance ();
  void print (Variant);
  variant scan ();
}
```

a|
== Mock object [pattern-type]#{structural-type}#

partially simulate object behaviour for testing
```
struct Shaman {
  Spell* castSpell () {
    openSpellbook ();
    Glyph* glyph = findSpell ();
    return glyph->cast ();
  }
}
struct ShamanMock {
  Spell* castSpell () {
    return new MightySpell ();
  }
}
```

a|
== Proxy [pattern-type]#{structural-type}#

add behaviour to existing interface
```
struct GuardedArmoryProxy : Armory {
  GuardedArmory (Armory* armory);
  void enter(Orc& orc) override {
    if (looksFriendly (orc))
      armory->enter (orc);
    else
      logFailure (orc);
  }
}
```

a|
== Service locator [pattern-type]#{structural-type}#

ease and cache service discovery
```
struct OrcIntelligence {
  static Area locate (Army army) {
    if (!lastSeen.contains (army)) {
      lastSeen[army] = lookFor (army);
    }
    return lastSeen[army];
  }
private:
  map<Army, Area> lastSeen;
}
```



// Behavioral


a|
== Blackboard [pattern-type]#{behavioral-type}#

integrate many modules in complex strategy
```
struct Intelligence {
  void updateDisposition () {
    // gather knowledge from sources
    each (scouts, Source::update(map));
    // process knowledge
    correctConflicts (map);
    // configure sources
    killLiars (scouts);
  }
private:
  Blackboard map;
  set<Source*> scouts;
}
```

a|
== Chain of responsibility [pattern-type]#{behavioral-type}#

unknown concrete handler for concrete request
```
struct OrcFighter : RequestHandler {
  OrcFighter (RequestHandler* next);
  void handle(Request req) override {
    if (req.type == Type::Attack) {
      attack ();
      // more logic
      if (++req.done > 10) return;
    }
    if (next) next->handle (req);
  }
}
```

a|
== Command [pattern-type]#{behavioral-type}#

hold all required data to perform/abort event
```
struct Move : Command {
  Move (Army army, Area from, Area to);
  void execute () override {
    from.removeArmy (army);
    to.addArmy (army);
  }
  void abort () override {
    to.removeArmy (army);
    from.addArmy (army);
  }
}
```

a|
== Delegation [pattern-type]#{behavioral-type}#

provide functionality via composition part
```
struct OrcFighter {
  Size weaponSize () const {
    return weapon.size ();
  }
}
```

a|
== Dependency injection [pattern-type]#{behavioral-type}#

use constructed dependency
```
struct OrcFighter {
  OrcFighter (AbstractArmor* armor);
  void hit (Damage damage) {
    health -= armor->reduce (damage);
  }
}
```

a|
== Feature toggle [pattern-type]#{behavioral-type}#

dynamically enable/disable code branches
```
struct OrcFighter {
  void attack () {
    if (FeatureToggle::isOn(Stealth)){
      hiddenAttack ();
    }
    else {
      simpleAttack ();
    }
  }
}
```

a|
== Intercepting filter [pattern-type]#{behavioral-type}#

add pre/post-processing to requests
```
struct FilterManager {
  FilterManager (Target* target);
  void handle (Request request) {
    each (filters,
      Filter::handle (request));
    target->deliver (request);
  }
private:
  list<Filter*> filters;
}
```

a|
== Interpreter [pattern-type]#{behavioral-type}#

handle AST of domain specific language
```
struct Plus : Expression {
  Plus (Expression& left, Expression& right);
  Value interpret () override {
    return left.interpret ()
      + right.interpret ();
  }
}
```

a|
== Iterator [pattern-type]#{behavioral-type}#

traverse container without knowing its structure
```
struct OrcIterator {
  OrcIterator& operator++ (); // next
  Orc& operator-> (); // value
  bool operator!= (const OrcIterator&) const;
}
for (OrcIterator it = army.begin (),
  end = army.end (); it != end; ++it) {
    it->attack ();
}
```

a|
== Mediator [pattern-type]#{behavioral-type}#

hide objects interacion details
```
struct OrcTrainingCamp {
  void train (Orc& old, Orc& young) {
    young.raiseSkill (old.skill ());
  }
}
```

a|
== Memento [pattern-type]#{behavioral-type}#

save/restore object's state
```
struct Orc {
  Memento state () {
    return Memento {this->health};
  }
  void restore (Memento memento) {
    this->health = memento.health;
  }
  class Memento {
    int health;
    friend class Orc;
  }
}
```

a|
== Method chaining [pattern-type]#{behavioral-type}#

multiple method calls in one expression
```
struct Orc {
  Orc& setName (Name name) {
    this->name = name;
    return *this;
  }
  Orc& setWeapon (Weapon);
}
Orc orc = Orc().setName ("Named")
  .setWeapon (Sword());
```

a|
== Null object [pattern-type]#{behavioral-type}#

specific object for empty (null) behaviour
```
struct FakeOrc : Orc {
  void attack () override {}
}
Orc* makeNewOrc () {
  if (!reachedLimit ()) return new Orc;
  return new FakeOrc;
}
```

a|
== Observer [pattern-type]#{behavioral-type}#

notify subscribers about publisher events
```
struct OrcCampObserver : Observer {
  void notify (Event& event) override {
    each (orcs, Orc::handle (event));
  }
}
struct OrcCamp : Observable {
  void addObserver (Observer) override;
  void dinnerTime () {
    each (observers,
      Observer::notify (DinnerEvent()));
  }
}
```

a|
== Servant [pattern-type]#{behavioral-type}#

add behaviour to other classes
```
struct Blacksmith {
  void sharpenWeapon (Fighter*);
}
```

a|
== Specification [pattern-type]#{behavioral-type}#

filter of validate objects with dynamic rules
```
struct BySkill : Specification {
  bool check (Orc& orc) override {
    return orc.gotSkill (Sneak);
  }
}
void recruit (Orc& orc) {
  auto filter = BySkill ().and (ByMana ());
  if (filter.check (orc)) {
    assignForImportantMission (orc);
  }
}
```

a|
== State [pattern-type]#{behavioral-type}#

behavior depends on finite internal states
```
struct Orc {
  void fear () {
    setState (new ScaredState(this));
  }
  void attack () {
    state->attack ();
  }
}
struct ScaredState : OrcState {
  void attack () override {
    owner->flee ();
  }
}
```

a|
== Strategy [pattern-type]#{behavioral-type}#

use group of interchangeable algorithms
```
struct Orc {
  void attack () {strategy->attack ();}
private:
  AttackStrategy* strategy;
}
struct Defensive : AttackStrategy {
  void attack () override {
    raiseShield ();
    swordAttack ();
  }
}
```

a|
== Template method [pattern-type]#{behavioral-type}#

override only part of algorithm
```
struct Orc {
  virtual Target* chooseTarget ();
  virtual hitTarget () = 0;
  void stepBack ();
  void attack () {
    auto target = chooseTarget ();
    hitTarget (target);
    stepBack ();
  }
}
```

a|
== Type tunnel [pattern-type]#{behavioral-type}#

unified processing of different types
```
struct Dragon {
  void eatImpl (Food);
  Food makeFood (Orc);
  Food makeFood (Dwarf);
  template<class T>
  void eat (T t) {
    eatImpl (makeFood (t));
  }
}
```

a|
== Visitor [pattern-type]#{behavioral-type}#

apply operation on object's elements
```
struct ArmyMeleePower : Visitor {
  void visit (Melee& orc) override {
    this->power += orc.power ();
  }
  void visit (Ranged& orc) override {}
}
struct Melee : Visitable {
  void accept (Visitor& visitor) override{
    visitor.visit (*this);
  }
}
```



// Concurrency


a|
== Active object [pattern-type]#{concurrency-type}#

separate execution and invocation threads
```
struct ActiveObject {
  void addCommand (Command command) {
    this->commands << command;
  }
  ~ActiveObject () {
    thread thread ([this]() {
      each (commands, Command::exec());
    }
    thread.join ();
  }
}
```

a|
== Async method call [pattern-type]#{concurrency-type}#

non-blocking method call in remote thread
```
struct Shaman {
  void resurrect (Creature& dead) {
    Finder& remote = astral.soul(dead);
    if (!remote.isReady ()) drink ();
    Soul& soul = astral.takeSoul(remote); // blocks
    dead.revive (soul);
  }
}
```

a|
== Balking [pattern-type]#{concurrency-type}#

ignore calls until ready
```
struct Barracks {
  Fighter* trainRecruit () {
    AutoLock lock;
    if (!gotRecruits ()) return {};
    return train (takeRecruit ());
  }
  void addRecruit (Recruit recruit) {
    AutoLock lock;
    addRecruit (recruit);
  }
}
```

a|
== Binding properties [pattern-type]#{concurrency-type}#

synchronize several properties
```
template<class T> struct Property<T> {
  void bind (Property<T>* other);
  void set (T value) {
    preventInfiniteRecursion ();
    other->set (value);
  }
}
house.isWarm.bind (&heater.isOn);
```

a|
== Blockchain [pattern-type]#{concurrency-type}#

ordered appendable chain of verified transactions
```
struct OrcHistorian : BlockchainNode {
  bool addLegend (Legend legend) {
    if (!verify (legend)) return false;
    bool accepted = all (others, &OrcHistorian::addLegend (legend))
    if (accepted) writeLegend (legend);
    return accepted;
  }
private:
  list<OrcHistorian> others;
}
```

a|
== Double-checked locking [pattern-type]#{concurrency-type}#

reduce locking overhead for conditional
```
struct Tavern {
  bool close () {
    if (state != Empty) return false;
    // be sure in interested case
    AutoLock lock;
    if (state == Empty) state = Closed;
  }
}
```

a|
== Guarded suspension [pattern-type]#{concurrency-type}#

block call until ready
```
struct Barracks {
  Fighter* trainRecruit () {
    while (true) {
      if (AutoLock lock, gotRecruits())
        return train (takeRecruit ());
      wait ();
    }
  }
  void addRecruit (Recruit recruit) {
    AutoLock lock;
    addRecruit (recruit);
  }
}
```

a|
== Join [pattern-type]#{concurrency-type}#

pipleine of sync/async messaging channels
```
struct Channel {
  void put (Message* message);
  Message* get ();
}
struct Forge : Pipe {
  void craft (Message* iron, Message* crafter);
}
Tavern ().when (minesChannel).and (craftersChannel).do (Forge::craft);
```

a|
== Lock (Mutex) [pattern-type]#{concurrency-type}#

block if resource is busy
```
struct Tavern {
  void enter () {
    lock.acquire (); // blocks if busy
    ++customers;
    lock.release ();
  }
  void leave () {
    lock.acquire (); // blocks if busy
    --customers;
    lock.release ();
  }
}
```

a|
== Monitor object [pattern-type]#{concurrency-type}#

allow conditional access to guarded resourse
```
struct Tavern {
  void enter () {
    lock.acquire ();
    while (isFull ()) {
      monitor.wait (lock, queue1); // release on sleep, acquire on wake
    }
    ++customers;
    lock.release ();
  }
  void leave () {
    AutoLock lock(this->lock)
    --customers;
    monitor.wakeOne (queue1);
  }
}
```

a|
== Proactor [pattern-type]#{concurrency-type}#

gather async requests and send to async handlers
```
struct Forge {
  void exec () {
    each (mines, Mine::startDig (), static_cast<OnDone> (Forge::craft));
  }
  void craft (Iron iron) {
    nextCrafter ()->asyncCraft (iron, static_cast<OnDone> (::deliver));
  }
}
```

a|
== Reactor [pattern-type]#{concurrency-type}#

gather async requests and send to sync handlers
```
struct Forge {
  void exec () {
    while (true) {
      for (Iron& i: getAllReadyIron()){
        craft (i);
      }
      waitForMoreIronReady (timeout);
    }
  }
  void craft (Iron iron) {
    ::deliver (nextCrafter ()->craft (iron));
  }
}
```

a|
== Read-write lock [pattern-type]#{concurrency-type}#

allow concurrent reads, but exclusive writes
```
struct Tavern {
  list<Customer> customers () const {
    rwLock.readAcquire (); // allow many
    auto result = customers;
    rwLock.readRelease ();
    return result;
  }
  void addCustomer (Customer customer) {
    rwLock.writeAcquire (); // allow one
    customers << customer;
    rwLock.writeRelease ();
  }
}
```

a|
== Scheduler [pattern-type]#{concurrency-type}#

control resource usage time
```
struct Forge : Scheduler {
  void requestAnvil (Crafter user) {
    plan.add (user);
  }
  void exec () {
    while (true) {
      Crafter& current = anvil.user();
      if (!current.isFinished ()) {
        current.pause ();
        plan.add (current);
      }
      Crafter& next = plan.takeNext ();
      anvil.setUser (next);
      waitForNextEvent (plan);
    }
  }
}
```

a|
== Thread pool [pattern-type]#{concurrency-type}#

execute task in idle thread from pool
```
using Staff = Thread;
struct Tavern {
  void addVisitor (Visitor visitor) {
    queue << new ServeEvent (visitor);
    if (gotIdle ()) processQueue();
  }
  void processQueue () {
    while (Staff* staff = nextIdle()) {
      if (Event* e = queue.takeNext ())
        staff.run (e);
      else break;
    }
  }
}
```


a|
== Active record [pattern-type]#{architectural-type}#

manupilate single row in database
```
struct Orc {
  void save () {
    run ("insert into orcs values(?,?)", id, name);
  }
  void remove () {
    run ("delete from orc where id = ?", id);
  }
  static Orc* find (Name name);
}
```

|
|
|
|

|===
