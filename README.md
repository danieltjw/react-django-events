# Events

**[Live App: https://eventsr.herokuapp.com](https://eventsr.herokuapp.com)**

Built with React, Redux, Materialize, RESTful API and Django.

![](img/big-screen.png "Big Screen")

---

### Media Auto Complete Search

<p align="center">
  <img src=img/auto-complete.png alt="Auto Complete"/>
</p>

Makes a call to the Open Movie Database API for auto complete suggestions.

Features:

- Delay (time since user stops typing) before making API call can be set to reduce no. of API calls at the cost of responsiveness
- Preloader to indicate loading of media details after selection (useful feedback for users on slower networks)
- Caching to further reduce API calls (FIFO arbitrary limit of 1000, 10mins expiry)
- Intelligently decides if images should be shown in autocomplete dropdown based on latest network speed

---

### Mobile Demo

<p align="center">
  <img src=img/mobile-demo.gif alt="Mobile Demo"/>
  <img src=img/small-screen.png alt="Small Screen"/>
</p>

---

### Responsive Design

Text (event's title and date) and components adapt to different display resolutions.

<p align="center">
  <img src=img/responsive-design.gif alt="Responsive Design"/>
</p>

---

### Ratings

Users can submit a rating for each event.

The overall rating from all users is only revealed once the users themselves have submitted a rating:

- Avoids priming user feedback with pre-existing ratings
- Encourages user participation by virtue of curiosity

<p align="center">
  <img src=img/events-rating.gif alt="Rating"/>
</p>
<p align="center">
  Ratings mouse-over, mouse-out & submit
</p>

---

### Countdown

<p align="center">
  <img src=img/events-countdown.gif alt="Countdown"/>
</p>

---

### Performance Optimization

<a href="https://danieltjw.github.io/react-django-events/html/eventsr.herokuapp.com_2019-07-05_12-00-56.html" target="_blank" rel="noopener">Lighthouse Report</a>

<p align="center">
  <img src=img/lighthouse-report.png alt="Lighthouse Report"/>
</p>

- Asynchronous resources loading
- Images Optimization
- Static files caching (Django and WhiteNoise)
- Pre-rendering

---

### SMS Notification Service

An optional SMS notification service can send out a feedback summary after specified interval using Django's post_save signal.

This settings can changed on the fly through heroku's config variable dashboard without needing to redeploy the app.

---

### Security

Links opened in new window have the [`rel="noopener"` attribute](https://developers.google.com/web/tools/lighthouse/audits/noopener).

---

### Privacy

Person's private information such as phone numbers are not revealed by the API.

---

### Django Admin

<p align="center">
  <img valign="top" src=img/events-django-admin-session.png alt="Django Admin Sessions"/>
  <img valign="top" src=img/events-django-admin-speakers.png alt="Django Admin Speakers"/>
</p>

---

### Testing with jest, enzyme, redux-mock-store

Testing redux actions & reducers, components snapshot testing

---

### Deployment in under 5 steps / mins

1. `npm test`
2. `npm run build` (creates 'build' directory and any postbuild steps such pre-rendering)
3. `git commit -a -m "hello world"` (commits the django directory, which includes react's 'build')
4. `git push heroku master` (heroku runs collectstatic and then deploys the django app which serves the react frontend)

App is now live!

---

### React Hooks

React Hooks, new to v16.8, are a functional way of writing components compared to class based ones.
Seems to be the direction React is moving strongly towards (though class-based components will still be supported).

```javascript
import React, { useEffect } from "react";
import { useSelector } from "react-redux";

export default function MovieDetail() {
  const movie = useSelector(state => state.movie);
  const movieKeys = useSelector(state => state.movieKeys);
  const loadingTitle = useSelector(state => state.loadingTitle);
  useDocumentTitle(movie, loadingTitle);

  // Order of the movieKeys affects the order the movie details displayed
  const defaultMovieKeys = [
    "Year",
    "Actors",
    "Director",
    "Plot",
    "Awards",
    "Writer",
    "Genre",
    "Released",
    "Runtime",
    "Rated",
    "Type",
    "Country",
    "Language",
    "Metascore",
    "imdbRating",
    "Website"
  ];

  function formatWebsite(key, movie) {
    const url = movie[key];
    const displayItem = (
      <p key={key}>
        <b>Website</b>:{" "}
        <a href={url} target="_blank" rel="noopener noreferrer">
          {url}
        </a>
      </p>
    );
    return displayItem;
  }

  function formatKeys(keys, movie) {
    if (keys.length === 0) {
      keys = defaultMovieKeys;
    }
    let displayList = [];
    for (const key of keys) {
      let displayItem = null;
      // API may have keys with ("N/A") values, those are not displayed
      if (movie[key] !== "N/A" && key in movie) {
        if (key === "Website") {
          displayItem = formatWebsite(key, movie);
        } else if (key === "imdbRating") {
          displayItem = (
            <p key={key}>
              <b>{key}</b>: {movie[key]} ({movie["imdbVotes"]} votes)
            </p>
          );
        } else if (key === "Plot") {
          displayItem = (
            <p key={key}>
              {" "}
              {movie[key]}
              <br />
            </p>
          );
        } else {
          displayItem = (
            <p key={key}>
              <b>{key}</b>: {movie[key]}
            </p>
          );
        }
        displayList.push(displayItem);
      }
    }
    return displayList;
  }

  if (typeof movie !== "undefined") {
    const movieDetails = formatKeys(movieKeys, movie);
    let image = "";
    if (movie.Poster !== "N/A") {
      image = (
        <img
          className="responsive-img materialboxed"
          src={movie.Poster.replace("http:", "https:")}
          alt={"Poster for " + movie.Title}
          data-caption={"Poster for " + movie.Title}
        />
      );
    }
    return (
      <div className="movie card" key={movie.imdbID}>
        <div className="movie-content row">
          <div className="center">
            <div className="col s6 offset-s3 m2 l2">
              <div className="poster">{image}</div>
            </div>
          </div>
          <div className="col s12 m10 l10" id="details">
            <b>
              <p className="flow-text">{movie.Title}</p>
            </b>
            {movieDetails}
          </div>
        </div>
      </div>
    );
  } else {
    return <div />;
  }
}

function useDocumentTitle(movie, loadingTitle) {
  useEffect(() => {
    if (movie) {
      document.title = movie.Title;
    } else if (loadingTitle) {
      document.title = loadingTitle;
    }
  }, [movie, loadingTitle]);
}
```
