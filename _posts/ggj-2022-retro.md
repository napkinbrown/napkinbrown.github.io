## Retro Thoughts
 - It was odd knowing the theme so far ahead of time
 - Working with a larger team is easier than I expected. The first game jam I did with around 5 friends who ranged from "could program" to "able to google stuff". It was a largely frusterating experience, especially for the members in the later camp. Additionally, around three or four of us ended up touching code in one way or another. Three or four developers not knowing what they're doing touching an untested repo under time crunch is a recipe for bugs,
 - Extra people also brings with it more need for team management
 - However, this time it nobody's first game, which smoothed over a lot of bumps. Additionally, we had clearly defined roles that we rarely strayed outside of. Also, when teammembers are silo'ed like this, it really doesn't increase the team management cost as much. Not that there aren't downsides to silo'ed teams
 - We ran into an issue we were warned about: because we were so silo'ed, we basically had our asset production side throwing assets at us from over the wall. For some things, this wasn't a huge issue: the person in charge of music dumped around 4-5 tracks around midway through the jam, which, I just threw all of in on shuffle (which wasn't a good idea for a few reasons, mainly because you really only hear the first 30 seconds of each track if you're lucky, but that's just another learning opportunity amirite)
 - We didn't communicate effectively to our artist, leading to her generating assets we didn't use due to time pressure, as well as having a a few art assets we needed but didn't know until too late.
 - Subscriber pattern is poggers, I don't know why Unity doesn't push event management as hard as say, Godot? While none of this should be a shock, having entities be able to subscribe to events made integrating work from mutliple developers trivial. I wrote a centralized EventManager that maintained a collection of events as well as some basic gameplay management jobs. We used the built-in C# delegate/events over UnityEvents, which seemed to be the right choice, although it lead to a little bit of hacky code since I didn't realize you couldn't trigger events from outside the class

 ```cs
  public delegate void DeathAction();
  public static event DeathAction OnDeath;
  public static void InvokeOnDeath()
  {
    float t;
    OnDeath();
  }

 ```

- The solution going forward is probably just have the entities who want to trigger game state changing events trigger their own event that the game state manager listens for. 
- There was a persistent question that was on our minds throughout most of the jam, which was: What does enterprise Unity development look like? A lot of our systems worked for the small slapdash project we worked on, but we could feel the seams of the code tighten as we got close to the end of the jam. The way we structured our work turned out well with a small project, but it would not withstand the complexities of a larger game and longer development lifespan. While there is a ton of Unity tutorials and guides, from my experience they tend to use wildly different and often contradicting coding patterns. It's tough to tell whether or not there is a "gold standard" for creating scalable Unity projects. On the flip side, while Godot doesn't have nearly the same level of documentation or learning materials, it's clear there are some rules of the engine they expect you to work within, namely, the hierarchacel systems and built-in event management.
- A good game jam game is equivalent to a bad normal game. It's possible to make jam game that's also a good normal game, but it requires a lot more skill than I currently possess. I think that it's fair or creatively or mentally to me to compare the game we made against the higher quality games made during the jam, but it doesn't feel good to create bad art.
- I think this jam also re-kindled my love for game development (not that I was a hot-shot game dev before this). Video games are a beautiful interactive medium and I forgot how much I loved them during my part-time school, full-time work grind. I'm looking forward to my next game project, but that will be something more long-term