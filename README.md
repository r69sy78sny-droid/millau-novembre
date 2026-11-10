# VU-mètre · Millau du 27 au 29 novembre 2026 (Fly Millau)

Site : https://r69sy78sny-droid.github.io/millau-novembre/

Une aiguille de **−180** (impossible) à **+180** (tu pourras voler) répond à une seule question :
*pourrai-je voler au moins une matinée à Millau entre le vendredi 27 et le dimanche 29 novembre ?*
Elle vaut `360 × P − 180`, où P est la probabilité d'avoir au moins 1 matinée volable sur 3.
Dérivé du VU-mètre du stage Fly Millau d'octobre : même moteur, climatologie et calibration refaites sur novembre.

## Comment l'aiguille est calculée

1. **Matinée volable** : au moins 2 h d'affilée volables entre 9 h et 13 h (limites des élèves de Fly Millau :
   vent ≤ 22 km/h, rafales ≤ 35 km/h, pas de vent de NE-E au-delà de 8 km/h, pas de nuage au déco, pas d'orage,
   vent à 850 hPa ≤ 45 km/h). Seuils dans `engine/config.js`, à faire valider par un moniteur.
2. **Prévisions** : 11 ensembles et 11 modèles déterministes via [Open-Meteo](https://open-meteo.com/).
3. **Calibration** : régression logistique ERA5 → station Météo-France de Millau, refaite sur les matinées de
   novembre 1993-2025. Vents des modèles ramenés à l'échelle de la station (×1,18 / rafales ×0,89, retrouvés
   sur novembre).
4. **Échéance** : au-delà d'une dizaine de jours, mélange avec la climatologie (a(L) = 1 / (1 + e^((L − 10)/2,5))).
5. **Combinaison** : 120 000 trajectoires de 3 matinées (système × membre × fenêtre climatologique du 20 au 30 novembre).
6. **Paraglidable** : second avis facultatif (secret `PARAGLIDABLE_KEY`).

## Mises à jour

`.github/workflows/aiguille.yml` tourne toutes les 20 minutes (chaque passage programme le suivant). Si l'aiguille
bouge de 30 points, un commentaire est posté sur l'issue d'alertes. Après les vols, écris dans cette issue
`volé 27`, `pas volé 28` ou `oublie 29`.

## En local

```bash
npm test
node scripts/update.mjs --dry
node scripts/build-climatology.mjs   # ~130 Mo de données Météo-France
python3 -m http.server 8803
```

Outil d'aide personnel : c'est toujours le moniteur de Fly Millau qui décide si l'on vole.
