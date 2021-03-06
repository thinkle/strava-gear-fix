<script>
  import { Router, Link, Route } from "svelte-routing";
  import Login from "./Login.svelte";
  let faq;
  let showInfo = false;
  import { scope, currentPath } from "./stores";
  export let url;
  let atIndex = true;
  $: atIndex = $currentPath == "/";
</script>

<main>
  <h1>
    {#if $currentPath.indexOf("haystack") > -1}
      Haystack Tool
      <span class="sub"
        >Can't remember how you got to that spot? Search your Strava history
        using a map
      </span>
    {:else if $currentPath.indexOf("time") > -1}
      Time Tally Tool
      <span class="sub">
        Tally up your time spent outside, by activity or by bike.
      </span>
    {:else if $currentPath.indexOf("bike") > -1}
      Bike Chooser
      <span class="sub"> See which bike you have for which ride. </span>
    {/if}
    <span
      class:badge={!atIndex}
      on:click={() => {
        showInfo = true;
      }}
      >Tom's Cycling Tools
      {#if !showInfo}
        <button class="fader">i</button>
      {/if}
    </span>
  </h1>
  <img
    class="stravaBranding"
    src="api_logo_pwrdBy_strava_horiz_gray.svg"
    alt="Powered by Strava"
  />

  {#if showInfo}
    <section>
      <h3>About</h3>
      {#if $currentPath == "/" || $currentPath.indexOf("haystack") > -1}
        <p>
          <b>Route Finder:</b> Drag location to a spot on the map, pick a threshhold,
          find the route that goes directly by that spot.
        </p>
      {/if}
      {#if $currentPath == "/" || $currentPath.indexOf("bikechooser") > -1}
        <p>
          <b>Bike Setter</b>: change which bike is associated with which ride.
          You can set up rules to have different defaults for virtual/real
          rides, or by speed or temperature. Right now, that just color-codes
          and makes it a bit quicker to change bikes to the default. Makes it
          easy to scan the list for mismatches as well.
          {#if $scope && $scope.indexOf("write") == -1}
            <b
              >Note: you have only given read-only permission to access the
              Strava API, so Bike Setter isn't currently available. Log out and
              log back in with <em>write</em> permission in order to use the bike
              setter tool.
            </b>
          {/if}
        </p>
      {/if}
      {#if $currentPath == "/" || $currentPath.indexOf("time") > -1}
        <p>
          <b>Time Tally</b>: I build this tool to help track how many hours I've
          spent outside as my family has been working on
          <a href="https://www.1000hoursoutside.com/">1,000 hours outside</a>.
        </p>
        <p>
          The tool also will let you track hours you've spent in each activity
          type and on each bike (or, presumably, pair of shoes? I don't know
          &mdash; I'm not a runner!).
        </p>
      {/if}
      <p><a href="#faq" on:click={() => (faq = true)}>FAQ</a></p>
      <button
        on:click={() => {
          showInfo = false;
        }}>❌</button
      >
    </section>
  {/if}
  <Login
    {url}
    onSelectCallback={() => {
      showInfo = false;
    }}
  />
  {#if faq}
    <div id="faq" class="modalBackground" on:click={() => (faq = false)}>
      <div class="modal" on:click={(e) => e.stopPropagation()}>
        <button
          style="position: fixed; top: 5px; right: 5px;"
          on:click={() => {
            faq = false;
          }}>❌</button
        >

        <dl>
          <dt>What data are you collecting about me?</dt>
          <dd>
            <p>None.</p>
            <p>
              When you log into strava, strava provides me a code. That code
              (your secret) plus my API key (my secret) go back to strava and
              get exchanged for a token which allows the code running in this
              webpage (in <b>your</b> browser) to access your data from strava.
            </p>
            <p>
              Your routes and profile info go straight from strava to your
              browser. I don't actually ever see them.
            </p>
            <p>
              I don't even store the magic token that allows access on my
              server: you store it in your browser.
            </p>
            <p>
              The only request you make that goes to my server is the request to
              get the token from strava, which has to go through me so that I
              don't expose my API key, which is what strava uses to limit how
              much I can use their service.
            </p>
          </dd>
          <dt>Can I support you?</dt>
          <dd>
            Sure! Buy me a coffee, or a nice pastry for my next ride, by
            pitching in at my paypal link.
            <form
              action="https://www.paypal.com/donate"
              method="post"
              target="_top"
            >
              <input
                type="hidden"
                name="hosted_button_id"
                value="JGAY7MS9ASP5A"
              />
              <input
                type="image"
                src="https://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif"
                border="0"
                name="submit"
                title="PayPal - The safer, easier way to pay online!"
                alt="Donate with PayPal button"
              />
              <img
                alt=""
                border="0"
                src="https://www.paypal.com/en_US/i/scr/pixel.gif"
                width="1"
                height="1"
              />
            </form>
          </dd>
          <dt>What plans do you have for the future?</dt>
          <dd>
            <p>
              I may try to automate the bike chooser. If I do, that would mean I
              would have to start using webhooks, which would mean Strava pings
              my app every time you post a new activity. I would also have to
              store information about your bike settings so that I could update
              them without you doing anything.
            </p>
          </dd>
        </dl>
      </div>
    </div>
  {/if}
</main>

<style>
  .stravaBranding {
    height: 24px;
  }
  main {
    text-align: center;
    padding: 15px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
    position: relative;
    margin-top: 15px;
    margin-bottom: 17px;
  }
  h1 .sub {
    position: absolute;
    bottom: -15px;
    left: 0;
    display: block;
    width: 100%;
    text-align: center;
    font-size: 15px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  h1 .badge {
    position: absolute;
    top: -10px;
    right: 0;
    text-align: right;
    font-size: 15px;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
  section {
    position: relative;
    padding: 25px;
    text-align: left;
    border: 1px solid #888;
    max-width: 55em;
    margin: auto;
  }
  section button {
    position: absolute;
    right: 3px;
    top: 3px;
  }
  button {
    border-radius: 50%;
    text-align: center;
    font-size: 12px;
    height: 24px;
    width: 24px;
    display: inline-flex;
    vertical-align: top;
    align-items: center;
    justify-items: center;
    align-content: center;
    justify-content: center;
  }

  .modalBackground {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background-color: #2227;
    z-index: 3;
  }
  .modal {
    margin: auto;
    max-width: 600px;
    background-color: #333;
    color: #ddd;
    padding: 1.5rem;
    z-index: 4;
  }

  section p {
    margin-bottom: 1em;
  }

  dl {
    text-align: left;
    font-size: large;
  }
  dt {
    font-weight: bold;
    margin-bottom: 1rem;
  }
  dd {
    margin-bottom: 2rem;
  }
  dl p {
    margin-bottom: 1rem;
  }

  .badge {
    font-size: xx-small;
  }
</style>
