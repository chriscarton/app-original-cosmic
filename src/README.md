# Encoder les fonts en base64

Par exemple pour qu'un titre ne s'affiche qu'une fois que la fonte est chargée. 
Voir https://transfonter.org/ qui propose cette option. 

# Problème de persistance des images 

Il se peut que l'attribut src persiste si l'on est renvoyé vers le même composant mais avec des données différentes. 

Voici comment régler ce problème : 

    componentWillUpdate(){
        console.log('componentWillUpdate!');

        let imgs = document.querySelectorAll('#singleProject img');

        if(imgs){
            imgs.forEach(function(img){
                img.removeAttribute('src')
            });
        }
    }

# Transition entre les routes avec react-pose 

    yarn add react-pose

Ajouter ceci

    import posed, { PoseGroup } from 'react-pose';

    const RouteContainer = posed.div({
    enter: { opacity: 1, delay: 300, beforeChildren: true },
    exit: { opacity: 0 }
    });

Modifier le render de App.js comme ceci : 
    
    render(){
        return (
            <Router basename={process.env.PUBLIC_URL}>
            <div className="App">
                <Header/>
                <Route
                render={({ location }) => (
                <main id="mainContent">
                    <PoseGroup>
                        <RouteContainer key={location.pathname}>
                        
                        <Switch location={location}>
                            <Route path="/" exact component={Homepage}/>
                            <Route path="/studio" exact component={Studiocontact} />
                            <Route path="/projets" exact component={AllProjects} />
                            <Route path="/projet/:slug" exact component={SingleProject} />

                            <Route path="/tests" exact component={Tests} />
                            {/* <Route component={NotFound} /> */}
                        </Switch>
                        </RouteContainer>
                    </PoseGroup>
                    
                </main>
                )}>
                </Route>

                <Footer/>
            </div>
            </Router>
        );
    }

**Attention** *RouteContainer* doit bien recevoir pathname (*key={location.pathname}*) et non pas *key* comme indiqué dans la documentation. 

Cette issue est documentée dans : 
https://github.com/Popmotion/popmotion/issues/535

# Problèmes avec les fontes

## Fonte Cocogoose 

La Cocogoose ne contient pas de chiffres !

Donc pour de l'intégration, voici l'astuce. 

    h1,h2,h3,h4,h5,h6{
        font-family: 'COCOGOOSE';
        .numbers{
            font-family: 'Ubuntu Mono';
        }
    }

Cette fonte est payante et elle coûte 25 bucks. 

https://www.zetafonts.com/cocogoose

# Faire une vidéo à partir d'un .gif

Chose que l'on ne fera plus. 

Lancer la commande : 

    ffmpeg -i animated.gif -movflags faststart -pix_fmt yuv420p -vf "scale=trunc(iw/2)*2:trunc(ih/2)*2" video.mp4

Sur *ocp.1.gif* on passe donc d'un gif de 28mo à une vidéo mp4 de 3.6mo

Ce lien explique tout : 

https://rigor.com/blog/optimizing-animated-gifs-with-html5-video

Du coup mon intégration comment ça se passe ?


# Redimensionner des images avec mogrify 

Redimensionne toutes les images jpg **qui font plus de 320px de large** à 320px de large dans un dossier 320 :

    mogrify -path 320 -resize 320x "320x>" *.jpg

Pareil pour 600 : 

    mogrify -path 600 -resize 600x "600x>" *.jpg

mogrify -path 1280 -resize 1280x "1280x>" *.jpg

convert -path 1920 -resize 1920x1920\> *.jpg:

# Deployer 

## Attention ne pas faire ce qui suit : 

    //https://medium.com/p/f694d46427c1/responses/show

    //package.json
    "homepage": "https://myapp.com/directory-name",

    //App.js
    <Router basename={'/directory-name'}>
        <Route path='/' component={Home} />
        ...
    </Router>

    //Update the routes
    <Router basename={'/subdirectory'}>
        <Route path={`${process.env.PUBLIC_URL}/`} component={Home} />
        <Route path={`${process.env.PUBLIC_URL}/news`} component={News} />
        <Route path={`${process.env.PUBLIC_URL}/about`} component={About} />
    </Router>

    //Update the Links
    <Link to={`${process.env.PUBLIC_URL}/page-path`}>…</Link>

## Mais simplement ceci : 

    //Dans package.json
    "homepage": "https://chriscarton.github.io/app-original-cosmic/",

**Important** : 

    Nom du dossier = Nom du repository

Dans App.js

    <Router basename={process.env.PUBLIC_URL}>

Ajouter pour les images et autres videos : 

    process.env.PUBLIC_URL

Comme ceci : 

    <img
        srcSet={item.cover.versions.map((w) => (
            process.env.PUBLIC_URL+'/img/projects/' + w + '/' + item.cover.src + ' ' + w + 'w'
        ))}
        src={process.env.PUBLIC_URL+`/img/projects/${item.cover.src}`}
        alt=""
    />

Et c'est tout, ça fonctionnera aussi bien en local que sur github pages ou dans un sous-dossier.

# Déployer dans GITHUB PAGES

    yarn add gh-pages 

Dans *package.json* ajouter : 

    "homepage":"https://chriscarton.github.io/nom-du-dossier"

Dans *package.json* scripts, modifier *deploy*

    "deploy":"gh-pages -d build" 

Et enfin : 

    yarn run deploy

# COUPER UNE VIDÉO AVEC ffmepg 


    https://medium.com/abraia/basic-video-editing-for-social-media-with-ffmpeg-commands-1e873801659

    ffmpeg -i sequenceacouper.avi -ss 00:39:45.00 -t 00:00:30.00 -c:v copy -c:a copy  nouvellesequence.avi

    ffmpeg -i paleo_128.avi -ss 00:00 -t 00:00:12 -c:v copy -c:a copy  paleo_128-10s.avi

    couper le son : 

    ffmpeg -i paleo_cut.mp4 -c copy -an paleo_no_sound.mp4

    //celle-ci marche pas
    ffmpeg -i paleo_no_sound.mp4 -vf scale=400:-1 paleo_resized.mp4

    ffmpeg -i paleo_no_sound.mp4 -filter:v scale=720:-1 -c:a copy paleo_resized.mp4

    ffmpeg -i paleo_no_sound.mp4 -c:v libx264 -b:v 1.5M -c:a aac -b:a 128k paleo_128.mp4

    ffmpeg -i paleo_128-10s.mp4 -vf scale=400:-1 paleo_scale.mp4

    ffmpeg -i paleo_128-10s.mp4 -vf "scale=600:-2" paleo_scale.mp4

    //OMG
    ffmpeg -i omg.mp4 -ss 03.00 -t 05.00 -c:v copy -c:a copy  omg_cut.mp4
    ffmpeg -i omg_cut.mp4 -c:v libx264 -b:v 1.5M -c:a aac -b:a 128k omg_nosound.mp4
    ffmpeg -i omg_cut.mp4 -vf "scale=800:-2" omg_resized.mp4

    ffmpeg -i omg_resized.mp4 -ss 00.00 -t 05.00 -c:v copy -c:a copy  omg_resized-2.mp4