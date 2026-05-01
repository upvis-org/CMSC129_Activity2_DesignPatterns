# Dateboxd: A UPV Dating App

## App Summary
**Dateboxd** is a dating app tailored for UPV students. While most dating app only allows the users to get to know their match through profile summaries and chats, Dateboxd allows you to check your match's vibe score from their previous matches. 

Before you even decide to meet at the lover's lane or commute to the city campus, you can check a match’s **Vibe Score**. This score is built from peer reviews of their **online-only interactions**. It’s basically a community-led "Vibe Check" to ensure you’re not wasting your time on low-effort matches, ghosters, or "catfishes."

### The Twist
This dating app is inspired by letterboxd, where instead of a movie, you can rate and review your previous matches. 
After you and your match chatted for a certain period, you can choose to vibe check or rate your match based on different optional categories, or you can leave a review about your experience with your match.

### The 5 Digital Vibe Categories:
1.  **Response Time (Ghost-Meter):** Does it take them 3-5 business days to reply, or is the energy consistent?
2.  **Quality of Conversation:** Is it just "kamusta?" and "hi," or do they actually know how to carry a conversation?
3.  **Profile Accuracy:** Does their digital persona match their actual vibe (and program/acad org)?
4.  **Digital Respect:** Do they stay respectful in the DMs, or are they sending unsolicited vibes?
5.  **Vibe Consistency:** Is the energy they give off in their bio the same energy you get in the chat?

### Leaving a Review
Other than rating them based on the given categories, users can also leave a review or anything they wanted other users to know about their match.

## Design Pattern Implementation
### 1. Creational Design Pattern
* **Name of Pattern:** Creational - Factory
* **Concept in Conyo:**

  So yung ginagawa ng Factory Design Pattern is ini-encapsulate niya yung object creation. Instead na ang app ang bahala mag-decide kung anong object ang gagawin every time, we use a ReviewFactory to handle that process.

  In Dateboxd, users can rate their matches using different categories such as Response Time, Conversation Quality, Profile Accuracy, Digital Respect, and Vibe Consistency.

  Instead of writing multiple if-else conditions in different parts of the app to create each type of review, the app simply calls the factory method and passes the category.

  Parang sinasabi lang ng app:
  “Factory, gumawa ka ng review object for this category.”

  Then the ReviewFactory na bahala mag-decide kung anong specific class (e.g., ResponseTimeReview, ConversationQualityReview, etc.) ang gagawin.
* **Visual Diagram:**

#### Without Factory
```mermaid
flowchart TD
A[User submits review] --> B{Category}
B -->|Response Time| C[ResponseTimeReview]
B -->|Conversation| D[ConversationQualityReview]
B -->|Profile| E[ProfileAccuracyReview]
B -->|Respect| F[DigitalRespectReview]
B -->|Vibe| G[VibeConsistencyReview]
```

#### With Factory
```mermaid
flowchart TD
A[User submits review] --> B[ReviewFactory]
B --> C{Determine Type}
C --> D[ResponseTimeReview]
C --> E[ConversationQualityReview]
C --> F[ProfileAccuracyReview]
C --> G[DigitalRespectReview]
C --> H[VibeConsistencyReview]
```

* **Why it Works Nga:**

  Without using the Factory Pattern, parang the app mismo yung nagha-handle lahat, so ang daming if-else logic scattered sa different parts of the system (like submitting reviews, editing ratings, and processing vibe scores). Medyo hassle kasi every time may gagawin ka, kailangan mo pa ulit i-check kung anong object yung icreate.

  This leads to several disadvantages:

  - duplicated logic across different features (like paulit-ulit lang talaga yung same code everywhere)
  - tight coupling between the app and specific review classes (super dependent yung app sa exact classes)
  - difficult maintenance when adding new categories (pag may bagong category, ang dami mong babaguhin, not just one place)

  In Dateboxd, since ang daming review categories, mas lalong nagiging messy yung system and mas mataas yung chance na magka-errors kapag nag-update ka ng app.

  With the Factory Pattern, mas clean na yung approach kasi all object creation is handled na by the ReviewFactory. So instead na ang app yung magde-decide, parang sinasabi lang niya, “Factory, ikaw na bahala dito.”

  This reduces coupling, removes repeated logic, and makes the system easier to maintain and extend. If may new category, isang place lang (yung factory) ang babaguhin mo instead of editing multiple parts of the application, which is mas safe and less prone to errors.
* **Pseudocode:**
```
interface Review {
    void submitReview();
}

class ResponseTimeReview implements Review {
    public void submitReview() {
        System.out.println("Processing response time review");
    }
}

class ConversationQualityReview implements Review {
    public void submitReview() {
        System.out.println("Processing conversation quality review");
    }
}

class ProfileAccuracyReview implements Review {
    public void submitReview() {
        System.out.println("Processing profile accuracy review");
    }
}

class DigitalRespectReview implements Review {
    public void submitReview() {
        System.out.println("Processing digital respect review");
    }
}

class VibeConsistencyReview implements Review {
    public void submitReview() {
        System.out.println("Processing vibe consistency review");
    }
}

class ReviewFactory {
    public static Review createReview(String type) {
        if (type.equals("response_time")) return new ResponseTimeReview();
        else if (type.equals("conversation")) return new ConversationQualityReview();
        else if (type.equals("profile")) return new ProfileAccuracyReview();
        else if (type.equals("respect")) return new DigitalRespectReview();
        else if (type.equals("vibe")) return new VibeConsistencyReview();
        else return null;
    }
}

class App {
    public static void submitUserReview(String type) {
        Review review = ReviewFactory.createReview(type);
        if (review != null) {
            review.submitReview();
        } else {
            System.out.println("Invalid review type");
        }
    }
}
```

### 2. Behavioral Design Pattern
* **Name of Pattern:** Behavioral - Strategy
* **Concept in Conyo:**

  Kasi sa Dateboxd, hindi lang isa way para i-compute ang Vibe Score ng users. Depende sa need ng app, puwedeng iba-iba ang method ng pagcalculate ng ratings. For example, puwedeng pantay-pantay ang weight ng all categories, puwede rin mas mabigat ang Digital Respect kaysa Response Time, or puwede rin mas mataas ang influence ng recent reviews kaysa old ones. Instead na gumawa tayo ng super daming if-else statements like if standard mode, if weighted mode, if trending mode, ginagamit natin si Strategy Pattern. Gumagawa lang tayo ng iba’t ibang scoring algorithms as separate classes, then puwede silang palitan anytime during runtime. Parang same goal, different diskarte lang siya. Goal = compute Vibe Score, diskarte = chosen strategy. Super flexible nito kasi madaling magpalit o magdagdag ng bagong scoring method without changing the main code.
  
* **Visual Diagram:**

#### Without Factory
```mermaid
flowchart TD
A[User submits review] --> B{Category?}
B -->|Response Time| C[Create ResponseTimeReview]
B -->|Conversation| D[Create ConversationQualityReview]
B -->|Profile| E[Create ProfileAccuracyReview]
B -->|Respect| F[Create DigitalRespectReview]
B -->|Vibe| G[Create VibeConsistencyReview]
```

#### With Factory
```mermaid
flowchart TD
A[User submits review] --> B[ReviewFactory]
B --> C{Determine Type}
C --> D[ResponseTimeReview]
C --> E[ConversationQualityReview]
C --> F[ProfileAccuracyReview]
C --> G[DigitalRespectReview]
C --> H[VibeConsistencyReview]
```

  
* **Why it Works Nga:**

  Without Strategy Pattern, lahat ng score computation logic magiging halo-halo sa isang malaking class. Kapag may bagong scoring system, edit ka nanaman ng core code, which can create bugs and stress.
With Strategy Pattern, bawat algorithm hiwalay ang responsibility. Ang BasicAverageStrategy focus lang sa pagkuha ng average score. Ang WeightedTrustStrategy focus sa pagbibigay higher weight sa trusted or verified reviews. Ang RecentBoostStrategy focus sa pagprioritize ng recent interactions. This follows the Open/Closed Principle kasi open for extension siya (add new strategies), but closed for modification (di gagalawin old code). Perfect ito for apps like Dateboxd na pwedeng mag evolve ang rating logic over time. Mas organized ang code, mas scalable ang system, at less iyak sa debugging.

  
* **Pseudocode:**
  
  ```
  # 1. THE STRATEGY INTERFACE
  # Defines a common interface for all vibe-calculation algorithms.
  INTERFACE ScoringStrategy:
      METHOD calculate(reviewss):
          // Every strategy must implement this logic


  # 2. CONCRETE STRATEGIES
  # Different algorithms for different needs.

  # Concrete Strategy 1
  CLASS BasicAverageStrategy IMPLEMENTS ScoreStrategy:
      FUNCTION calculate(reviews):
          RETURN average of all review scores

  # Concrete Strategy 2
  CLASS WeightedTrustStrategy IMPLEMENTS ScoreStrategy:
      FUNCTION calculate(reviews):
          total = 0
          weightSum = 0

          FOR each review IN reviews:
              weight = review.trust_level
              total += review.score * weight
              weightSum += weight

          RETURN total / weightSum

  # Concrete Strategy 3
  CLASS RecentBoostStrategy IMPLEMENTS ScoreStrategy:
      FUNCTION calculate(reviews):
          total = 0
          weightSum = 0

          FOR each review IN reviews:
              IF review.is_recent:
                  weight = 2
              ELSE:
                  weight = 1

              total += review.score * weight
              weightSum += weight

          RETURN total / weightSum

  # 3. THE CONTEXT
  # This is the class the App UI interacts with. 
  # It doesn't know HOW the score is calculated, only that it IS calculated.
  CLASS VibeCalculatorContext:
      PRIVATE strategy: ScoringStrategy

      # Allows the app to change scoring logic on the fly
      METHOD set_strategy(new_strategy):
          self.strategy = new_strategy

      METHOD get_score(user_data):
          RETURN self.strategy.calculate(user_data)

  # 4. IMPLEMENTATION EXAMPLE
  # Client-side usage in the Dateboxd App
  calculator = NEW VibeCalculatorContext()

  # User wants a standard vibe check
  calculator.set_strategy(NEW BasicAverageStrategy())
  PRINT "Standard Score: " + calculator.get_score(match_ratings)

  # Admin wants to flag "red flag" behavior by prioritizing Respect scores
  calculator.set_strategy(NEW SafetyFirstStrategy())
  PRINT "Safety-Adjusted Score: " + calculator.get_score(match_ratings)

  ```
  
### 3. Structural Design Pattern
* **Name of Pattern:** Structural - Decorator
* **Concept in Conyo:**

  Kasi nga optional only ang categorical ratings in Dateboxd, it is bagay talaga to make gamit Decorator Pattern to implement our feature. We start with the Base Review and wrap it layer by layer gamit ang decorators. It's like similar to making halo-halo where you can make pili the toppings you want to add. Like, you can make lagay sago if you want it in your halo halo or you not make lagay beans if it you don't like it. So in our Dateboxd, the user can just pili if gusto nila irate ang quality conversation, we can just make wrap our base review with qualityConversationDecorator. If they want to leave a review, the program will just wrap it with a review decorator. They are not made pilit to rate all categories or to bigay a review.
  

The base review ay foundation lang siya, and we just make wrap it with a specific decorator that the user wanted to implement.
* **Visual Diagram:**
* **Why it Works Nga:**

  Without our pinakamamahal na decorator, we need to make iba't ibang classes for the categories pati narin ang kanilang combinations na magmemake result on class explosion which is so hirap talaga to maintain sa isang dating app. Yung ating decorator makes our system to be very flexible talaga kasi we only need to wrap our base review to make dagdag the categories na want ng users irate, or if gusto nila magbigay ng review. This also adheres to the isa sa SOLID principles, yung Single Responsibility Principle kung saan each decorator only make focus sa kaniyang implementation, like yung profileAccuracyDecorator only make focus sa pagmanage ng pag-implement ng profile accuracy category, and so on, like gets ba? This will also make our buhay easier kung may idadagdag tayo na categories or ways to vibe check our matches like if magdagdag tayo ng tags na functionality other than the categories or reviews.
* **Pseudocode:**
```
# 1. THE BASE INTERFACE
# This defines the "contract" that all reviews and decorators must follow.
CLASS VibeComponent:
    FUNCTION get_vibe_summary():
        // Returns the string description of the vibe check
    FUNCTION get_total_score():
        // Returns the accumulated numeric rating

# 2. THE CONCRETE COMPONENT (The Foundation)
# The bare minimum review object before any categories are added.
CLASS BaseReview IMPLEMENTS VibeComponent:
    CONSTRUCTOR(match_id):
        self.match_id = match_id
        self.base_score = 0 // Base review starts with no score

    FUNCTION get_vibe_summary():
        RETURN "Vibe check for " + self.match_id

    FUNCTION get_total_score():
        RETURN self.base_score

# 3. THE BASE DECORATOR
# The wrapper that delegates calls to the wrapped object.
CLASS ReviewDecorator IMPLEMENTS VibeComponent:
    CONSTRUCTOR(wrapped_vibe):
        self.wrapped_vibe = wrapped_vibe

    FUNCTION get_vibe_summary():
        RETURN self.wrapped_vibe.get_vibe_summary()

    FUNCTION get_total_score():
        RETURN self.wrapped_vibe.get_total_score()

# 4. SPECIFIC CATEGORY DECORATORS (The Layers)

# Category 1: Response Time (Ghost-Meter)
CLASS ResponseTimeDecorator EXTENDS ReviewDecorator:
    CONSTRUCTOR(wrapped_vibe, score):
        SUPER(wrapped_vibe)
        self.score = score

    FUNCTION get_vibe_summary():
        RETURN self.wrapped_vibe.get_vibe_summary() + " | Response: " + self.score + "/5"

    FUNCTION get_total_score():
        RETURN self.wrapped_vibe.get_total_score() + self.score

# Category 2: Quality of Conversation
CLASS ConvQualityDecorator EXTENDS ReviewDecorator:
    CONSTRUCTOR(wrapped_vibe, score):
        SUPER(wrapped_vibe)
        self.score = score

    FUNCTION get_vibe_summary():
        RETURN self.wrapped_vibe.get_vibe_summary() + " | Chat Quality: " + self.score + "/5"

    FUNCTION get_total_score():
        RETURN self.wrapped_vibe.get_total_score() + self.score

# Category 3: Profile Accuracy
CLASS ProfileAccuracyDecorator EXTENDS ReviewDecorator:
    CONSTRUCTOR(wrapped_vibe, score):
        SUPER(wrapped_vibe)
        self.score = score

    FUNCTION get_vibe_summary():
        RETURN self.wrapped_vibe.get_vibe_summary() + " | Accuracy: " + self.score + "/5"

    FUNCTION get_total_score():
        RETURN self.wrapped_vibe.get_total_score() + self.score

# Category 4: Digital Respect
CLASS DigitalRespectDecorator EXTENDS ReviewDecorator:
    CONSTRUCTOR(wrapped_vibe, score):
        SUPER(wrapped_vibe)
        self.score = score

    FUNCTION get_vibe_summary():
        RETURN self.wrapped_vibe.get_vibe_summary() + " | Respect: " + self.score + "/5"

    FUNCTION get_total_score():
        RETURN self.wrapped_vibe.get_total_score() + self.score

# Category 5: Vibe Consistency
CLASS VibeConsistencyDecorator EXTENDS ReviewDecorator:
    CONSTRUCTOR(wrapped_vibe, score):
        SUPER(wrapped_vibe)
        self.score = score

    FUNCTION get_vibe_summary():
        RETURN self.wrapped_vibe.get_vibe_summary() + " | Consistency: " + self.score + "/5"

    FUNCTION get_total_score():
        RETURN self.wrapped_vibe.get_total_score() + self.score

# Extra Feature: Letterboxd-style Written Review
CLASS WrittenReviewDecorator EXTENDS ReviewDecorator:
    CONSTRUCTOR(wrapped_vibe, review_text):
        SUPER(wrapped_vibe)
        self.review_text = review_text

    FUNCTION get_vibe_summary():
        // Stacks the text at the end of the summary
        RETURN self.wrapped_vibe.get_vibe_summary() + " [Review: " + self.review_text + "]"

    FUNCTION get_total_score():
        // Written reviews don't add to the numeric score
        RETURN self.wrapped_vibe.get_total_score()

# 5. USAGE
// 1. Start with the base
vibe_check = BaseReview("UPV_Match_2026")

// 2. Wrap only the categories the user chose
IF user_rated_response:
    vibe_check = ResponseTimeDecorator(vibe_check, 5)

IF user_rated_respect:
    vibe_check = DigitalRespectDecorator(vibe_check, 4)

IF user_left_comment:
    vibe_check = WrittenReviewDecorator(vibe_check, "Super green flag!")

// 3. Output the final stacked result
PRINT vibe_check.get_vibe_summary()
PRINT "Total Score: " + vibe_check.get_total_score()
```





