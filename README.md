# -Data-Analytics---Data-Challenge-part2-cinema-reporting

As part of your film analysis, you'd like to calculate the latest box-office statistics.
box-office figures. To do this, you'll need to build a Dashboard containing the KPIs you consider relevant.

##### Data exploring and data cleaning
Nous commençons par importer chaque tableau à partir de fichiers CSV dans notre Power BI Desktop.
Ensuite, nous utilisons les fonctions de profilage des colonnes pour vérifier la granularité des données.
Après, on a la table **tblCast** qui contient des colonnes vides, qu'on va supprimer.
Après, on est resté avec la Colonne 1, qu'on avait besoin de manipuler aussi, en séparant les données de la colonne en utilisant le délimiter ',' en utilisant la fonctionnalité 'Fractionner une colonne'.
Après, on vérfie les données de chaque colonne pour voir si tout est bien en place, ensuite on définit la première ligne as a header.

Au niveau de la table **tblDirector** on a la colonne DirectorDOB qui est de type Text, on va dans un premier teps la mettre en format DateTime (pour ne pas générer d'erreur), ensuite on la convertera en format Date.
D'ailleurs on a aussi modifier le type de la colonne DirectorID de TEXT à INTEGER.

De même pour la table **tblActor**, on a aussi la colonne ActorDOB qui est de type TEXT, et ensuite on l'a modifié pour qu'elle soit d'abord dans un format DateTime, puis la mettre dans un format Date.

**NB :** Le fait de passer de DateTime à Date diminue la complexité de nos données, vu que dans le contexte actuel, on a pas besoin de l'information temporelle (horaire) pour faire nos analyses.
De plus, moins de données à traiter peut améliorer les performances, en plus on a la facilité de l'agrégation et la comparaison des données dans un contexte journalier, plutôt à une échelle plus fine, comme les heures et les minutes.

Dans la même mesure, dans la table **tblFilm** on a la colonne FilmReleaseDate qui est de type DateTime et on va la convertir en type Date.

C'est bon fermer et appliquer les modifications.

##### Data Model Construction
Une fois le data cleaning a été fait, on va maintenant élaborer le modèle de données.

Voici les différentes relations qu'on a pu créer dans le Data Model.
- **tblActor** à **tblCast**:
  - tblActor[ActorID] -> tblCast[CastActorID]
  - Relation : Un acteur peut avoir plusieurs rôles dans plusieurs films.
    
- **tblFilm** à **tblCast** :
  - tblFilm[FilmID] -> tblCast[CastFilmID]
  - Relation : Un film peut avoir plusieurs acteurs.
 
- **tblCertificate** à **tblFilm** :
  - tblCertificate[CertificateID] -> tblFilm[FilmCertificateID]
  - Relation : Un certificat peut être associé à plusieurs films.
 
- **tblCountry** à **tblFilm** :
  - tblCountry[CountryID] -> tblFilm[FilmCountryID]
  - Relation : Un pays peut produire plusieurs films.
 
- **tblDirector** à **tblFilm** :
  - tblDirector[DirectorID] -> tblFilm[FilmDirectorID]
  - Relation : Un réalisateur peut diriger plusieurs films.

- **tblLanguage** à **tblFilm** :
  - tblLanguage[LanguageID] -> tblFilm[FilmLanguageID]
  - Relation : Une langue peut être utilisée dans plusieurs films.

- **tblStudio** à **tblFilm** :
  - tblStudio[StudioID] -> tblFilm[FilmStudioID]
  - Relation : Un studio peut produire plusieurs films.


