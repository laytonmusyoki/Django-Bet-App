## NewBetApp
  
Betting aplication based on Django.   
Application uses the http://www.football-data.org API server for football data.
API provides competitions, teams, fixtures, teams etc.
Program allows user to bet fixtures. User can bet home win, draw or away win. 
Program schedules fixtures, automatically checks if fixture have started/finished and 
calculates bet payout (if bet has been won). Application also calculates odds of fixtures
based on current league table. Odds are recalculated after each matchday.
Automatic checks are made by means of Celery periodic tasks (also support of Kronos is provided - in cron.py file).
After making bet, confirmation email is sent to user.
Mail sending was made as a asynchronous task by means of Celery. Also history of teams standings 
is provided. Standings are stored as dictionary on redis server, and are shared through REST api.
On basis of standings received from API server, on team details page you can see plot 
with history of team standings up to current matchday. 
  
Steps to setup application:
* register on http://www.football-data.org and get your api key.  
* in module api_connection.py find function url_conn and assign your key to api_key variable.  
* migrate db   
* create superuser  
* migrate celery tables by typing in manage.py migrate djcelery
* run redis server
* run rabbitmq server
* start celery worker by typing in manage.py celeryd --verbosity=2
* start celery beat to register tasks to RabbitMQ by typing in manage.py celerybeat --verbosity=2 
* run Django development server
* login as superuser
* go to localhost:8000/add_competitions/2017 and check competitions that you want to follow/bet  
* now you can create app_user using register button and bet fixtures  
  
Fixtures are updated once every 3 minutes automatically.

Default localhost sites:  
* localhost:8000/add_competitions/{year} page with available competitions for given year,  
only superuser can add competitions.  
* localhost:8000/admin admin panel  
* localhost:8000/ main page with listed competitions  
* localhost:8000/competition/{id} page with listed fixtures for competition with given id  
* localhost:8000/login displays form to login to app  
* localhost:8000/logout view that logs out current user
* localhost:8000/register displays form that adds new app user that can make bets
* localhost:8000/bet_fixture/{id} displays form that enables betting fixture with given id  
* localhost:8000/account_details/{id} displays page with account details such as amount of cash, pending/finished bets etc for app user with given id
* localhost:8000/show_team/{id} displays detailed data such as team home/away fixtures etc for team with given id in db.  
* localhost:8000/competition_table/{id} displays league table for competition with given {id} in DB
* localhost:8000/team_standings/{competition_id}/{team_id} returns json with two lists (for team with {competition_id} and {team_id} in DB), first is matchdays, second is standings 
