# Data-Analytics---Data-Challenge-part2-cinema-reporting

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


##### DAX measures construction and visual creation
Voici déjà les premières mesures DAX qui me semble cohérentes et pertinentes dans nos analyses.

Tout d'abord pour les bonnes pratiques on va commencer par créer une table supplémentaire qui s'appelle **Mesures** qui va contenir bien évidemment les mesures qu'on créera
```
Mesures = { "Mesures" }
```
  
**Nombre total des films**
```
TotalFilms =
COUNT ( 'tblFilm'[FilmID] )
```

**Nombre de films par réalisteur**
```
FilmsByDirector =
COUNTROWS ( 'tblFilm' )
```

**Budget Total des Films**
```
TotalBudget =
SUM ( 'tblFilm'[FilmBudgetDollars] )
```

**Revenus Totaux au Box-Office**
```
TotalBoxOffice =
SUM ( 'tblFilm'[FilmBoxOfficeDollars] )
```

**Nombre d'Oscars Gagnés par Film**
```
TotalOscarsWon =
SUM ( 'tblFilm'[FilmOscarWins] )
```

**Nombre de Nominations aux Oscars par Film**
```
TotalOscarNominations =
SUM ( 'tblFilm'[FilmOscarNominations] )
```

**Ratio Budget/Box-Office**
```
BoxOfficeToBudgetRatio =
DIVIDE (
    SUM ( 'tblFilm'[FilmBoxOfficeDollars] ),
    SUM ( 'tblFilm'[FilmBudgetDollars] ),
    0
)
```

**Nombre de Films par Certificat**
```
FilmsByCertificate =
COUNT ( 'tblFilm'[FilmID] )
```

**Nombre de Films par Pays**
```
FilmsByCountry =
COUNT ( 'tblFilm'[FilmID] )
```

**Nombre de Films par Studio**
```
FilmsByStudio =
COUNT ( 'tblFilm'[FilmID] )
```

**Nombre de Films par Langue**
```
FilmsByLanguage =
COUNT( 'tblFilm'[FilmID] )
```

**Durée Moyenne des Films**
```
AverageFilmDuration =
AVERAGE ( 'tblFilm'[FilmRunTimeMinutes] )
```

**Nombre de Films par Acteur**
```
FilmsByActor =
COUNTROWS( 'tblCast' )
```

**Budget Total des Films par Réalisateur**
```
TotalBudgetByDirector =
CALCULATE (
    SUM ( 'tblFilm'[FilmBudgetDollars] ),
    ALLEXCEPT ( 'tblDirector', 'tblDirector'[DirectorID] )
)
```

**Revenus Totaux au Box-Office par Studio**
```
TotalBoxOfficeByStudio =
CALCULATE (
    SUM ( 'tblFilm'[FilmBoxOfficeDollars] ),
    ALLEXCEPT ( 'tblStudio', 'tblStudio'[StudioID] )
)
```

**Commentaire :** La fonction est utilisée pour supprimer tous les filtres de la table spécifiée, à l'exception des colonnes indiquées. Cela permet de calculer des agrégations en ignorant certains filtres appliqués par le contexte des lignes, tout en respectant les filtres sur les colonnes spécifiées.


**Parmi les Best practices :** La création de la table Calendrier pour faciliter les analyses temporelles en fournissant des informations supplémentaires sur les dates, telles que les années, les trimestres, les mois, les jours de la semaine, etc.
```
calendrier = 
VAR MinDate = 
    MINX(
        UNION(
            SELECTCOLUMNS('tblFilm', "Date", 'tblFilm'[FilmReleaseDate]),
            SELECTCOLUMNS('tblActor', "Date", 'tblActor'[ActorDOB]),
            SELECTCOLUMNS('tblDirector', "Date", 'tblDirector'[DirectorDOB])
        ),
        [Date]
    )
VAR MaxDate = 
    MAXX(
        UNION(
            SELECTCOLUMNS('tblFilm', "Date", 'tblFilm'[FilmReleaseDate]),
            SELECTCOLUMNS('tblActor', "Date", 'tblActor'[ActorDOB]),
            SELECTCOLUMNS('tblDirector', "Date", 'tblDirector'[DirectorDOB])
        ),
        [Date]
    )
RETURN
ADDCOLUMNS (
    CALENDAR (DATE(YEAR(MinDate), MONTH(MinDate), DAY(MinDate)), DATE(YEAR(MaxDate), MONTH(MaxDate), DAY(MaxDate))),
    "Year", YEAR([Date]),
    "MonthNumber", MONTH([Date]),
    "MonthName", FORMAT([Date], "MMMM"),
    "Quarter", "Q" & QUARTER([Date]),
    "Weekday", WEEKDAY([Date]),
    "WeekdayName", FORMAT([Date], "dddd"),
    "YearMonth", FORMAT([Date], "YYYY-MM")
)


```
Donc, une fois la table **Calendrier** est créée on va par la suite, la lier aux différentes tables dans le DataModel afin de pouvoir mener des analyses temporelles.


**Pain Point :** En fait, une fois nous avions voulu lier la table **Calendrier** avec les 2 tables **tblActor** et **tblDirector** vous avons rencontré l'errue de l'ambiguité du chemin. Cela signifie que Power BI détecte plusieurs chemins possibles pour atteindre une table à partir d'une autre, ce qui peut créer des conflits lors du filtrage des données.


**Solution :**
On va rendre les 2 relations suivantes inactives et si on aura des utiliser on pourra passer par la formule DAX **USERELATIONSHIP**.


Voici quelques mesures supplémentaires pour nos analyses.

On va commencer par créer des segments de filtrages.

Maintenant, on va créer des mesures DAX qu'on va mettre dans des visuels en carte.

**Total des Revenus au Box-Office**
```
TotarRevenueBoxOffice =
SUM ( 'tblFilm'[FilmBoxOfficeDollars] )
```

**Nombre Total de Nominations aux Oscars :**
```
TotalOscarNominations =
SUM ( 'tblFilm'[FilmOscarNominations] )
```

**Nombre de Films récompensés par des OSCARS**
```
TotalOscarWinningFilms = 
CALCULATE(
    COUNT('tblFilm'[FilmID]),
    'tblFilm'[FilmOscarWins] > 0
)
```


**Budget Moyen des Films**

```
AverageFilmBudget =
AVERAGE ( 'tblFilm'[FilmBudgetDollars] )
```
