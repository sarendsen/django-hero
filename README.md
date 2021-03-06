Django Hero
=========

Hero is a powerful, extensible, reusable application that provides
support for awarding "Achievements" to users in Django.

Installation
------------

Add hero to your INSTALLED_APPS:

    INSTALLED_APPS = [
      # ...
      "hero",
    ]

Add the autodiscover function to your urls.py for your convenience.

    # Register achievements
    import hero
    hero.autodiscover()

Optional: Hook up hero to your URLconf for some default views.

    urlpatterns = patterns("",
      # ...
      url(r"^achievements/", include("hero.urls"))
    )

AVAILABLE SETTINGS
------------

ACHIEVEMENT_LEVELS  (=((1, "Bronze"), (2, "Silver"), (3, "Gold"),(4, "Diamond"),))

ACHIEVEMENT_IMAGE_LOCKED  (='achievements/images/default-locked.jpg')

ACHIEVEMENT_IMAGE_UNLOCKED  (='achievements/images/default-unlocked.jpg')

ACHIEVEMENT_IMAGE_SECRET  (='achievements/images/default-secret.jpg')

USAGE
------------

Start by creating a achievements.py file in your app/project. The autodiscover function will pick this up.
Here you will define your custom achievements.

A achievement object might look like this:

    from hero import AchievementMeta
    from hero.models import AchievementUnlocked
    
    class TestAchievement(AchievementBase):
      '''
      Achievement attribute's
      '''
      id             = "comments_50"
      title          = "50 comments"
      description    = "User has posted 50 comments"
      secret         = False
      invisible      = False
      image_locked   = 'achievements/images/default-locked.jpg'
      image_unlocked = 'achievements/images/default-unlocked.jpg'
      image_secret   = 'achievements/images/default-hidden.jpg'

      '''
      Event's on wich this achievement should be called
      '''
      events = [
        "points_awarded",
      ]
      
      def unlock(self, **state):
        '''
        Validates unlock requirements
        '''
        user = state["user"]
        points = 5000
        if points == 5000:
          return AchievementUnlocked(level=1)

There are a few relevant attributes and methods here.

*   attribute:: id :: required

    The unique identifier for the 'Achievement', it should never change.
    
*   attribute:: title :: required

    Title for this achievement

*   attribute:: description :: optional

    Description for this achievement

*   attribute:: secret :: optional

    The achievement is visible for a user but does not reveal its title, description, etc untill the user unlocks it.

*   attribute:: invisible :: optional

    Invisible achievement. Invisible achievements should not be revealed untill a user unlocked it, neither does it shows as a secret achievement in listings.
    
*   attribute:: image_locked :: optional

    Override default locked achievement image

*   attribute:: image_unlocked :: optional

    Override default unlocked achievement image

*   attribute:: image_secret :: optional

    Override default hidden achievement image
    
*   attribute:: events :: optional

    A list of events that can possibly trigger this achievement to be unlocked.

*   method:: unlock :: required

    This method returns whether or not this achievement should be unlocked for a user.
    'state' is guaranteed to have a "user" key, as well as any other
    custom data you provide. It should return either a AchievementUnlocked instance, or 'None'.

Now just call the acchievements.unlock method for an event. 
Any achievement registered with the given event will get it's "unlock" method called and unlock it for a user if validates.

    import hero

    hero.achievements.unlock("points_awarded", user=request.user) 
    

Achievement's are saved to the DB aswell. This way you can use Django's ORM to query achievements.

- Sjoerd Arendsen