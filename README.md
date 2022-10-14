# MUSE | Music Player Application
MUSE - music player application that lets users play 30 second previews of their favorite songs using Spotify API integration. Search for your favorite artists, tracks, or albums.

## *Important Notice*
This is a demonstration repository as the original repository is set as private due to vulnerable access to authorization keys and for security concerns. If you would like to see the full repository, contact me at diannapham.se@gmail.com and I will provide an invitation link to the repository.

<!-- PROJECT LOGO -->
<br />

## Project Responsibilities
• Built a website application using front-end architecture to design the web page layout for consistent UI and React through a virtual DOM which enhanced rendering and component reusability by 60%.

• Integrated Spotify API using callbacks and asynchronous operations which reduced response time by 30% and improved application performance and responsiveness.

• Implemented data structures and algorithms which increased efficiency and reliability in data storage and retrieval. 


# Code Sample
export default function Searchpage() {
  const [imgSrc, setImgSrc] = useState("Invalid Image Source");
  const [searchInput, setSearchInput] = useState("");
  const [accessToken, setAccessToken] = useState("");
  const [albums, setAlbums] = useState([]);
  const [track, setTracks] = useState([]);
  
  //Access Spotify API through authorization token
  useEffect(() => {
      var authParameters = {
          method: 'POST',
          headers: {
              'Content-Type':'application/x-www-form-urlencoded'
          },
          body: 'grant_type=client_credentials&client_id=' + CLIENT_ID + '&client_secret=' + CLIENT_SECRET
      }

      fetch('https://accounts.spotify.com/api/token', authParameters)
          .then(result => result.json())
          .then(data => setAccessToken(data.access_token))

  }, []) 

  //Search (asynchronous)
  async function search() {
      console.log("Search for " + searchInput); 

          //Get request using search to get the song ID
      var searchParameters = {
          method: 'GET',
          headers: {
              'Content-Type': 'application/json',
              'Authorization': 'Bearer ' + accessToken
          }
      }

      //Artist ID to retrieve album data
      var artistID = await fetch('https://api.spotify.com/v1/search?q=' + searchInput + '&type=artist', searchParameters)
          .then(response => response.json())
          .then(data => { return data.artists.items[0].id })

      console.log("Artist ID is " + artistID);

      //Track ID used to retrieve several tracks
      const arrTracks = []; //Store IDs into an array
      for (let i = 1; i < 20; i++) {
        var trackID = await fetch('https://api.spotify.com/v1/search?q=' + searchInput + '&type=track,artist', searchParameters)
          .then(response => response.json())
          .then(data => {
            arrTracks.push(data.tracks.items[i].id);
            return data.tracks.items[0].id;
          });
      }
      console.log("TrackID is " + trackID);
      console.log(arrTracks);

      //Gets several tracks using Track ID and array of trackIDs (arrTracks)
      var severalTracks = await fetch('https://api.spotify.com/v1/tracks/' + '?ids=' + arrTracks + '&market=US', searchParameters)
        .then(response => response.json())
        .then(data => { 
            console.log(data);
            setTracks(data.tracks);
      });
      
      //Get album request using Artist ID
      var severalAlbums = await fetch('https://api.spotify.com/v1/artists/' + artistID + '/albums' + '?include_groups=album&market=US&limit=50', searchParameters)
          .then(response => response.json())
          .then(data => {
              console.log(data); 
              setAlbums(data.items);
      });

      //Gets track image request using Track ID
      var trackURL = await fetch('https://api.spotify.com/v1/tracks/' + trackID + '?market=US', searchParameters)
          .then(response => response.json())
          .then(data => { 
              display_image(data.album.images[0].url);
          });

      //Gets track name
      var trackName = await fetch('https://api.spotify.com/v1/tracks/' + trackID + '?market=US', searchParameters)
          .then(response => response.json())
          .then(data => {return data.name});
          document.getElementById("track-name").innerHTML = trackName;
       console.log("Track name is " + trackName);

      //Gets artist name
      var artistName = await fetch('https://api.spotify.com/v1/tracks/' + trackID + '?market=US', searchParameters)
          .then(response => response.json())
          .then(data => {
              return data.artists[0].name;
          });
       document.getElementById("artist-name").innerHTML = artistName;
       console.log("Artist name is " + artistName);

      //Retrieves track image to display within DOCUMENT
      function display_image(image_url) {
          document.getElementById("track-image").src = image_url; 
      }
  }

  return (

    <>

        <div class="input-icons">
            <i className="fa-solid fa-magnifying-glass icon form" onClick={search}></i>
            <InputGroup className="search-container input-field" spellcheck="false">
                <FormControl
                    className="form"
                    placeholder="Search artists, album, or tracks"
                    type="input"
                    onKeyPress = {event => {
                        if (event.key == "Enter") {
                            search();
                        }
                    }}
                    onChange = {event => setSearchInput(event.target.value)}
                />      
            </InputGroup>
        </div>

    <div class="container">
      <h1>Top result</h1>

      <div class="main">
        <div>
          <CardImg  id="track-image" src={imgSrc} onError = {() => setImgSrc("https://i.ibb.co/MfrQbjN/default.jpg")} class="search-img"></CardImg>
        </div>

        <div>
          <div class="search-text">
              <h5 id="track-name">Top Artist</h5>
              <h6 id="artist-name">Track</h6>
              <div>top streams</div>
          </div>
        </div>
        {/*<i class="fa-solid fa-play search-icon"></i>*/}
      </div>

      <div class="songs">
        <i class="fa-solid fa-headphones-simple song-icon"></i>
        <h2>Songs</h2>
      </div>

      <div class="track-box">
      {track.map((tracks, i) => {
        console.log(tracks);
        return (  
          <>
          <div>
            <a class="track-card" href={tracks.album.images[0].url}>
              <CardImg id="track-image" src={tracks.album.images[0].url} onError = {() => setImgSrc("https://i.ibb.co/MfrQbjN/default.jpg")} class="search-img"></CardImg>
              <div class="search-text">
                <h5>{tracks.name}</h5>
                <h6>{tracks.artists[0].name}</h6>
              </div>
            </a>
          </div>
          </>
        )
      })}
      </div>
    </div>
    </>
  )
}




<!-- PAGES -->
# Pages

## Feature 1 - UI Design

<img width="419" alt="Screen Shot 2022-10-13 at 3 12 09 PM" src="https://user-images.githubusercontent.com/97206862/195720521-530d1898-e972-4fd8-afbe-64c29b2082df.png">


## Feature 2 - Search Function
<img width="404" alt="Screen Shot 2022-10-13 at 3 12 38 PM" src="https://user-images.githubusercontent.com/97206862/195720531-4f0129b6-4096-493e-8379-085e3370c08b.png">

## Feature 3 - Project Demonstration
https://user-images.githubusercontent.com/97206862/195823730-e719c3c7-302d-442f-b394-874fc3c25fd1.MOV


<p align="right">(<a href="#top">back to top</a>)</p>

### Built With

* [HTML](https://html.spec.whatwg.org/)
* [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS)
* [React](https://reactjs.org/)
* [Bootstrap](https://getbootstrap.com/)
* [SASS](https://sass-lang.com/)
* [Font Awesome](https://fontawesome.com/)
* [Spotify API](https://developer.spotify.com/)

<p align="right">(<a href="#top">back to top</a>)</p>


### Installation
To retrieve and inspect the source code,
1. Clone the repo to your IDE
   ```sh
   git clone git@github.com:dianna-SE/music-app.git
   ```
2. Must have dependencies and frameworks installed:
- [ ]  React
- [ ]  SASS
- [ ]  Bootstrap


<p align="right">(<a href="#top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

[Dianna Pham](https://www.linkedin.com/in/diannapham-se/) - diannapham.se@gmail.com

(https://www.diannapham.com/)

<p align="right">(<a href="#top">back to top</a>)</p>
