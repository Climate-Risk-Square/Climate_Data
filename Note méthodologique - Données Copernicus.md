# Note méthodologique sur l'usage des données copernicus¶

## Présentation de copernicus
Copernicus est un programme d'observation de la terre coordonné par l'Union européenne en partenariat avec ces états membres et différentes institutions metéoroligiques et climatiques. Il offre gratuitement et librement l'acces à des informations climatiques et météorologiques basées sur l'observation de la terre récupérées par satérlite ou in situ (des données non spaciales).

## Récupération des données

La récupération des données copernicus se fait par API. Les étapes à suire avant l'import des données se trouve ici: https://cds.climate.copernicus.eu/api-how-to
Copernicus dispose aussi d'une chaine Youtube avec des videos présentant les étapes à suivre notamment comment installer l'API: https://www.youtube.com/watch?v=NHbvfggMC10

Le programme pour l'API permettant de récupérer les données est disponible sur copernicus. Une fois le jeu de données choisit, il faut se rendre dans l'onglet "download data", selectionner les caractéristiques souhaitées puis selectionner "show API request" en bas de la page.

Le code prends la forme suivante:
```
import cdsapi

c = cdsapi.Client()

c.retrieve(
    'projections-cmip6',
    {
        'format': 'zip',
        'area': [
            52, -10, 40,
            10,
        ],
        'date': '1990-01-01/2021-12-31',
        'temporal_resolution': 'daily',
        'experiment': 'historical',
        'level': 'single_levels',
        'variable': 'precipitation',
        'model': 'cnrm_esm2_1',
    },
    'PrecipitationRéduit.zip')
```

Ce programme permet d'importer des données issues de la base CMIP6. On récupère plus particulièrement la variable precipitation pour une experimentation de type historique via le modèle cnrm_esm2_1 et sur une période allant de 1er janvier 1990 au 31 décembre 2021. Les données sont aussi récupérés sur une zone géogrphqiue données dont le perimetre est definit via les coordonées GPS.

Les données sont importées en format zip dans le même dossier ou se trouve le fichier d'appel API. Le dossier contiendra au minimum 3 fichiers dont un fichier en NetCDF (.nc), c'est dans ce document que se trouve les données. Il arrive parfois que les données soient récupérées dans plusieurs fichiers nc.

## Lecture des données

La lecture des données se fait comme suit:

```
import xarray as xr
import pandas as pd

precipitationR = 'C:/Users/asmynour.youssouf_ad/Documents/PrecipitationReduit/pr_day_CNRM-ESM2-1_historical_r1i1p1f2_gr_19900101-20141231_v20181206.nc'
ds_precipitationR = xr.open_dataset(precipitationR)
df_precipitationR = ds_precipitationR.to_dataframe()

```

Dans certains cas il est possible que le programme retourne une erreur:

```
precipitation = 'C:/Users/asmynour.youssouf_ad/Documents/Precipitation2/pr_day_CNRM-ESM2-1_historical_r1i1p1f2_gr_19820101-20021120_v20181206.nc'
ds_precipitation = xr.open_dataset(precipitation)
df_precipitation2= ds_precipitation.to_dataframe()
```

Dans le cas de ce type d'erreur une première solution est de modifier la structure des données récupérées, on peut au choix:

* Réduire le périmètre des données en précisant autant que possible le la zone géographique et la période dont on a besoin
* Récupérer une granularité plus faible, copernicus propose des données journalières et des données mensuelles. Lorsqu'il faut données sur une période et une zone géographique large, si les données menssuelles suffisent il peut être judicieu de s'en contenter.

Si aucune de ces deux options ne suffit, ou n'est possible, on peut encore passer nos données en data frame en plusieurs fois.
La table contenant les données de précipitation contient deux variables: pr et time_bonde. On peut importer les deux variables séparément puis les concaténer

```
df_precipitation_pr= ds_precipitation["pr"].to_dataframe()
df_precipitation_bound= ds_precipitation["time_bounds"].to_dataframe()
```

Nota bene : Ces lignes ne fonctionnent pas sur Jupiter mais fonctionne sur Spyder 

