# Jumbotron

Welcome to Jumbotron! Jumbotron is a SvelteKit site that can be used for Hack Club Satellite events to display scheduled events, announcements, presentations, and more. It also includes a live share feature that allows participants to follow along with what's on the big screen through their own devices. The current stage of the project is a demonstration of the core functionalities, as specific theming hasn't been released for V5 and proper beta testing couldn't be completed in the time-frame. Extensive future updates are planned.

If you aren't familiar with Hack Club, nor Hack Club Satellite events, check out hackclub.com and campfire.hackclub.com!

## How Jumbotron was made

Jumbotron is a static SvelteKit project that works through a web browser

## How to use Jumbotron 

Jumbotron is available at hacklyn.city/jumbotron; we recommend that Chrome is used on a laptop for Jumbotron. Liveshare should work for participants on most devices.

How you'll proceed depends on what you're testing. If you want to test Liveshare from a participant perspective, choose the 2nd branch.

### Organizer Console and Projection

From the main page, you should choose the "Control Window" link. If you don't understand certain features, toggle the "Show Tutorial" button to enable explanations. If you wish to change the city/event-name, you can modify the name in the link. 

Example: <i>hacklyn.city/jumbotron/leafyland/control</i> -> <i>hacklyn.cityjumbotron/nyc/control</i>

The event that you input must have been an existing Campfire event.

### Participant Liveshare

From the main page, you should choose the "Liveshare" link. From here, you will be prompted for a 5-digit code. Unless a Control Window is already setup, you may need to create your own (To do this, follow the steps above for Organizer Console in another tab or window). At the bottom of the Organizer Console, the option to enable Liveshare and copy the 5-digit code can be found. 

Once setup, the participant device will listen for new changes on the Jumbotron. These include the following at the current stage of development:

- Events, without countdowns
- Announcements
- Displayed PDFs and Presentations (button with a link will be provided)



