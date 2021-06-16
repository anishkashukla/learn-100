### Clickjacking (UI redressing)

### What is clickjacking ?
````
Clickjacking is an interface-based attack in which a user is tricked into clicking on actionable content on a hidden website by clicking on some other content in a decoy website. Consider the following example:

A web user accesses a decoy website (perhaps this is a link provided by an email) and clicks on a button to win a prize. 
Unknowingly, they have been deceived by an attacker into pressing an alternative hidden button and this results in the payment of an account on another site. 
This is an example of a clickjacking attack. The technique depends upon the incorporation of an invisible, actionable web page (or multiple pages) containing a button or hidden link, say, within an iframe. 
The iframe is overlaid on top of the user's anticipated decoy web page content. 
This attack differs from a CSRF attack in that the user is required to perform an action such as a button click whereas a CSRF attack depends upon forging an entire request without the user's knowledge or input.
Protection against CSRF attacks is often provided by the use of a CSRF token: a session-specific, single-use number or nonce.
Clickjacking attacks are not mitigated by the CSRF token as a target session is established with content loaded from an authentic website and with all requests happening on-domain. 
CSRF tokens are placed into requests and passed to the server as part of a normally behaved session. The difference compared to a normal user session is that the process occurs within a hidden iframe.
````
### How to construct a basic clickjacking attack
````
Clickjacking attacks use CSS to create and manipulate layers. The attacker incorporates the target website as an iframe layer overlaid on the decoy website. An example using the style tag and parameters is as follows:

<head>
  <style>
    #target_website {
      position:relative;
      width:128px;
      height:128px;
      opacity:0.00001;
      z-index:2;
      }
    #decoy_website {
      position:absolute;
      width:300px;
      height:400px;
      z-index:1;
      }
  </style>
</head>
...
<body>
  <div id="decoy_website">
  ...decoy web content here...
  </div>
  <iframe id="target_website" src="https://vulnerable-website.com">
  </iframe>
</body>

The target website iframe is positioned within the browser so that there is a precise overlap of the target action with the decoy website using appropriate width and height position values. 
Absolute and relative position values are used to ensure that the target website accurately overlaps the decoy regardless of screen size, browser type and platform. 
The z-index determines the stacking order of the iframe and website layers. The opacity value is defined as 0.0 (or close to 0.0) so that the iframe content is transparent to the user. 
Browser clickjacking protection might apply threshold-based iframe transparency detection (for example, Chrome version 76 includes this behavior but Firefox does not). 
The attacker selects opacity values so that the desired effect is achieved without triggering protection behaviors.
````
### Clickjacking with prefilled form input
````
Some websites that require form completion and submission permit prepopulation of form inputs using GET parameters prior to submission. 
Other websites might require text before form submission. As GET values form part of the URL then the target URL can be modified to incorporate values of the attacker's choosing and the transparent "submit" button is overlaid on the decoy site as in the basic clickjacking example.
````
### Frame busting scripts
````
Clickjacking attacks are possible whenever websites can be framed. Therefore, preventative techniques are based upon restricting the framing capability for websites. 
A common client-side protection enacted through the web browser is to use frame busting or frame breaking scripts. 
These can be implemented via proprietary browser JavaScript add-ons or extensions such as NoScript. Scripts are often crafted so that they perform some or all of the following behaviors:

check and enforce that the current application window is the main or top window,
make all frames visible, prevent clicking on invisible frames, intercept and flag potential clickjacking attacks to the user.
Frame busting techniques are often browser and platform specific and because of the flexibility of HTML they can usually be circumvented by attackers. 
As frame busters are JavaScript then the browser's security settings may prevent their operation or indeed the browser might not even support JavaScript. 
An effective attacker workaround against frame busters is to use the HTML5 iframe sandbox attribute. 
When this is set with the allow-forms or allow-scripts values and the allow-top-navigation value is omitted then the frame buster script can be neutralized as the iframe cannot check whether or not it is the top window:

<iframe id="victim_website" src="https://victim-website.com" sandbox="allow-forms"></iframe>

Both the allow-forms and allow-scripts values permit the specified actions within the iframe but top-level navigation is disabled. This inhibits frame busting behaviors while allowing functionality within the targeted site.
````