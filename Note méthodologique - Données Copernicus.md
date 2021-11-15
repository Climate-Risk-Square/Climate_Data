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

