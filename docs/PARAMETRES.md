# Paramètres v2 et sources

Fiche demandée par l'issue #2. Elle indique, pour chaque paramètre du modèle v2, ce que des sources publiques appuient, contredisent ou laissent ouvert. Elle ne change aucune valeur du code et ne valide aucun résultat.

Recherche documentaire du 25 octobre 2026. Configuration visée : `train_exit.py --v2 --paris` (`train_exit.py:135-137`), qui diffère des valeurs par défaut d'`ExitConfig` (24 groupes, `H=25`, `ring=8`). Le profil « difficile » n'existe qu'à l'évaluation (`evaluate_exit_v2.py:97-99`).

## Statuts

- **observé** : mesuré ou décrit directement dans au moins une source portant sur une foule urbaine ou une nasse, avec date et lieu ;
- **inféré** : déduit par calcul, conversion ou analogie à partir de sources qui ne portent pas directement sur ce contexte ;
- **arbitraire** : aucune source ne fixe la valeur ; choix de modélisation, d'apprentissage ou choix normatif ;
- **contredit** : des sources applicables décrivent des valeurs ou des mécanismes hors de ce que le modèle suppose.

Un statut « observé » sur un cas ne vaut pas pour toutes les nasses. Les cas documentés sont surtout des nasses contestées (rapports d'ONG, contentieux, enquêtes parlementaires) ; les nasses courtes et sans incident, si elles existent, ne sont décrites nulle part.

## Échelle implicite

Le code ne déclare qu'une unité. Les deux autres viennent de la v1 et ne sont pas reprises explicitement par la v2.

| Grandeur | Valeur | Origine |
|---|---|---|
| Case | 10 m × 10 m | `osm_map.py:16`, `data/README.md` |
| Pas | 10 s | commentaire v1, `train.py:138` (`comm_delay=3` y vaut « 30 s ») |
| Groupe | 130 personnes | champ v1 `group_size`, `env.py:69-70`, non utilisé par `exit_env.py` |

Conversions utilisées ci-dessous : n groupes sur une case font 1,3·n p/m² ; 1 case par pas fait 1 m/s ; 6 pas font une minute, 360 pas une heure ; k groupes par pas à travers une face de 10 m font 1,3·k p/(m·s).

## Lecture rapide

- L'échelle spatiale et la physique de passage ont des appuis : la largeur médiane d'une voie parisienne est proche d'une case, et 1,3 p/(m·s) est la valeur des guides pour un passage libre.
- Le mouvement ne tient pas compte de la densité : vitesse constante, cases sans plafond, rues sans capacité. La littérature piétonne le contredit.
- Le temps et la police sont contredits. Dans les nasses documentées, la première sortie collective arrive après environ 50 minutes à plusieurs heures, la sortie est filtrée (contrôle, fouille, interpellation) et la police réagit à la foule. Sur 24 minutes, seul `sealed` ressemble aux cas décrits.
- Aucune source, dans aucun pays, ne donne de taux d'acceptation d'une demande de sortie, de délai de réponse, de largeur de point de sortie, de densité mesurée dans une nasse parisienne ni de délai humain de diffusion d'une information entre manifestants. Ces valeurs restent à balayer.
- Les coefficients de récompense sont des choix normatifs ; les sources éclairent leur forme, pas leur valeur.

## Fiches par paramètre

Les identifiants entre crochets renvoient au registre en fin de document.

### Échelle et espace

| Paramètre | Valeur v2 | Statut | Appuis et contradictions | Plage fondée | Limites |
|---|---|---|---|---|---|
| Taille de case | 10 m | inféré | Largeur officielle médiane de 1 956 voies de dix arrondissements : 11,5 m ; rues 12 m, avenues 23 m, boulevards 35 m, passages 4 m, impasses 5 m ; 31 % des voies et 15 % du linéaire sous 10 m [PA1]. Grille grossière admissible si les flux entre cellules sont bornés [PH12] ; automates piétons à 0,4 m [PH11]. | 10 m pour le réseau principal ; 5 m pour représenter passages et impasses | Largeur entre façades, pas largeur praticable ; mesure locale [PA1] |
| Durée d'un pas | 10 s (implicite) | inféré | 1 case par pas = 1 m/s ; vitesse libre 1,34 m/s [PH2, PH4] ; règle Δt = ΔL / v_libre, soit 7,5 s pour 10 m [PH12]. | 7,5 à 10 s ; 10 s plafonne la vitesse à 1 m/s | Ondes stop-and-go et colmatage aux portes, à l'échelle de la seconde, non représentables |
| Taille d'un groupe | 130 personnes (implicite) | arbitraire | Plus de 10 % des manifestants viennent seuls (69 manifestations, 8 pays, France non incluse) [PA8] ; groupes sociaux de 2 à 5 personnes dans les foules [PA9]. | À nommer « paquet de 130 personnes », sans portée sociale | Aucune taille de groupe mesurée dans une manifestation française |
| Fenêtre | 650 m (`H=65`) | arbitraire | Aucune source. | — | — |
| Taille du cordon | carré de côté ≈ 190 m (`ring=9`) | inféré | Place d'Italie, 16/11/2019 : place d'environ 200 m dont chaque débouché est barré [FR4, PA2] ; places de test de 215 à 283 m [PA2]. Nasses de rue de mars 2023 : d'une vingtaine à plus de 100 personnes sur un tronçon [FR7]. | 150 à 300 m pour une nasse de place ; quelques dizaines de mètres pour une nasse de rue | Aucune surface encerclée chiffrée dans les sources |
| Forme et position du cordon | anneau carré centré | contredit à République et Nation | L'anneau coupe République dans sa longueur ; à Nation il suit la chaussée annulaire au lieu des débouchés ; ligne de cordon de 310 m à Bastille, 410 m à République, 610 m à Nation [PA3]. Les nasses de place décrites barrent les débouchés [FR4]. | Cordon aux débouchés de chaque place | Un seul cas parisien de nasse de place décrit en détail |
| Zone de départ | rayon 60 m (`spawn_radius=6`) | arbitraire | Densité initiale résultante 0,40 à 0,75 p/m² de rue aux trois lieux de test [PA3] ; foule en marche estimée visuellement à « légèrement plus d'une » personne par m² [PA7]. | Densité initiale à tirer entre 0,5 et 2 p/m² | Aucune densité mesurée dans une nasse |
| Recadrages d'entraînement | ≥ 50 % de rue au centre, à plus de 1 km des lieux de test | arbitraire | Aucune source. Nation n'a que 49 % de rue au centre ; la place d'Italie reste dans l'entraînement [PA3]. | — | Ne teste pas la généralisation hors du tissu haussmannien |
| Lieux de test | République, Bastille, Nation | observé (lieux de manifestation) | Cortège Bastille–Nation [PA4] ; encerclement rapporté par la presse à République le 29/11/2015 [PA5]. | — | Aucune nasse chronométrée à ces trois places |
| Orientation aléatoire | quart de tour tiré | arbitraire | Augmentation de données. | — | — |

### Foule et mouvement

| Paramètre | Valeur v2 | Statut | Appuis et contradictions | Plage fondée | Limites |
|---|---|---|---|---|---|
| Effectif | 48 groupes ≈ 6 240 personnes | inféré (borne haute) | une vingtaine à plus de 100 (nasses de rue, Paris 2023) [FR7] ; ≈ 600 (Bellecour, 2010) [FR8] ; ≈ 300 (New York, 2020) [IN10] ; 4 000 à 5 000 (Londres, 2009) [IN2, IN3]. | Balayer au moins 300, 1 000, 3 000 et 6 000 | Aucun comptage indépendant dans une nasse parisienne |
| Groupes identiques et indivisibles | 48 × 130 | contredit | Petits groupes [PA8, PA9] ; blocs de tailles très inégales dans un même cortège [PA6] ; tri par catégorie de personnes à la sortie [IN1, IN4]. | Petits groupes tirés d'une distribution, regroupés en blocs divisibles | Aucune distribution mesurée en France |
| Vitesse | 1 m/s quelle que soit la densité | contredit | Paramétrage de Weidmann [PH1, PH2] : 0,90 m/s à 1 groupe par case, 0,43 à 2, 0,17 à 3, ≈ 0 au-delà de 4,2 (5,4 p/m²). Vitesses désirées 1,0 à 1,6 m/s [PH15]. | v(ρ) = 1,34·[1 − exp(−1,913·(1/ρ − 1/5,4))] | Synthèse multinationale ; aucune mesure sur des manifestants |
| Groupes par case | illimité | contredit | Densité de blocage 5,4 p/m² [PH2] ; maxima locaux au-delà de 10 p/m² à Jamarat [PH6]. Le code autorise 48 groupes sur une case (62 p/m²). | Plafond dur de 6 à 7 groupes par case | — |
| Seuil de densité `cap` | 3 groupes par case = 3,9 p/m² | inféré | Seuils de congestion : 3,55 p/m² (SFPE) et 3,5 (OMI) [PH10] ; 4 p/m² pour une foule en mouvement (Green Guide) [PH4]. Danger plus haut : 7 p/m² (Fruin) [PH5]. Maxima locaux voisins du double de la moyenne [PH6]. | 3 à 4 p/m² pour le début du risque ; second seuil vers 5 à 6 p/m² | Moyenne sur 100 m² ; aucune densité parisienne instrumentée |
| Largeur d'une ouverture | 1 case (10 m) | arbitraire | Imposée par la grille. Aucune largeur de point de sortie chiffrée. Unité de passage réglementaire de 0,60 m en ERP [PH14]. | Paramètre de largeur indépendant de la grille | La largeur ne limite pas une sortie filtrée |
| Capacité d'une ouverture (`gate_capacity`) | 1 groupe par pas = 13 p/s = 780 p/min | contredit comme débit de sortie de nasse | Comme passage libre, conforme aux guides : 1,30 (SFPE), 1,23 (Weidmann) p/(m·s) [PH3] ; 82 p/m/min, soit 1,37 p/(m·s) (Green Guide, 5e éd.) [PH4]. Comme sortie de nasse, contredit : sorties filtrées partout [FR4, FR7, IN1, IN9] ; le juge à Oxford Circus estime qu'une libération sans fouille aurait pris « plus de vingt minutes » [IN1] ; ≈ 1,4 à 3,4 p/min par point de sortie à Toronto et Francfort (calcul sur les chronologies) [IN6, IN9]. | Passage libre : 0,8 à 2,0 p/(m·s) × largeur. Sortie filtrée : de l'ordre de 1 à 16 p/min par point de sortie (calcul) | Aucune mesure de débit à travers une brèche de cordon ; nombre de files de contrôle inconnu |
| Capacité entre cases ordinaires | aucune | contredit | Débit maximal de 1,2 à 2,0 p/(m·s) [PH3] ; le code laisse passer 1,3·n p/(m·s). Le contre-flux réduit la capacité [PH7]. | ≈ 1 à 1,5 groupe par pas par face de 10 m (modèle de type PedCTM [PH12]) | — |

### Temps et police

Aucun texte français ne chiffre la durée d'une nasse, le délai avant ouverture ni le nombre de sorties. Le Conseil d'État a annulé en 2021 le point du schéma national du maintien de l'ordre (SNMO) sur l'encerclement [FR1]. La version de décembre 2021, validée en 2023, impose de « ménager un point de sortie contrôlé » « dès que les circonstances de l'ordre public le permettent », pour une durée « strictement nécessaire et proportionnée » ; ce point de sortie n'autorise pas, à lui seul, de contrôle d'identité hors de l'article 78-2 du code de procédure pénale [FR2]. La police nationale définit l'encerclement comme une retenue « pour une durée non déterminée » [FR3].

| Paramètre | Valeur v2 | Statut | Appuis et contradictions | Plage fondée | Limites |
|---|---|---|---|---|---|
| Durée d'épisode | 144 pas = 24 min | contredit | Durées documentées de l'ordre de l'heure à 13 h (tableau des cas). | 1 à 13 h (360 à 4 680 pas), ou déclarer les 24 min comme phase initiale | Cas biaisés vers les nasses contestées |
| Nombre d'ouvertures | jusqu'à 3 (`gate_count=3`) | inféré | « Un point de sortie contrôlé », au singulier [FR2] ; une sortie à Bellecour [FR9] ; un ou deux points à l'étranger [IN1, IN2, IN9]. | 0 à 2, le plus souvent 1 | Aucun cas de trois sorties simultanées |
| Ouvertures cachées jusqu'à l'ouverture | canal « passages » nul en v2 | observé (contraire à la norme) | « Les personnes n'étaient pas informées d'une sortie » [FR4] ; première annonce 1 h 40 après la fermeture à Oxford Circus [IN1]. Contre-exemple : annonce d'un point de passage à Francfort [IN9]. | — | Fréquence non mesurée |
| Tirage des scénarios | uniforme, 25 % chacun | arbitraire | Aucun recensement des nasses parisiennes. | Pondérer vers `sealed` et vers un mode « interpellation » | — |
| `sealed` | jamais d'ouverture | observé sur 24 min | Aucune nasse datée ne s'ouvre collectivement en 24 min ; plusieurs se terminent par l'interpellation de tous [IN6, IN10, IN11, IN12]. | — | L'issue réelle est l'interpellation ou la sortie filtrée, pas le maintien indéfini |
| `delayed` | ouverture après 2 à 8 min (difficile : 10 à 15 min), qui reste ouverte | contredit | Sortie « négociée » à 13 h 57, 51 min après la dernière sommation (pont de Sully, 2019) [FR15] ; nasse hermétique à 13 h 48 et ouverture signalée à 15 h 49 (place d'Italie, 2019) [FR4] ; libération finale à partir de 20 h 02 à Oxford Circus, après plusieurs reports [IN1]. | Première ouverture collective de 50 min à plusieurs heures ; ouverture intermittente | Trois cas français chronométrés |
| `responsive` : demande au cordon | groupe adjacent à une ouverture | observé (mécanisme), le plus souvent refusé | Les personnes « se voyaient opposer un refus lorsqu'elles se risquaient à s'approcher des cordons » [FR4]. Canaux réels liés à un rôle : officiers de liaison avec les organisateurs [FR19] et équipes de liaison et d'information prévues par le SNMO [FR17], référents presse [FR21], policiers de ligne vers le commandement [IN6]. | — | Aucun nombre de demandes publié |
| Délai de réponse | 30 s à 2 min (difficile : 3 à 5 min) | arbitraire | Aucun délai publié. Décisions collectives en dizaines de minutes à heures [FR4, FR15, IN1]. | Minutes à heures, à balayer | — |
| Probabilité d'acceptation | U[0,1 ; 0,7] par épisode (difficile : U[0 ; 0,2]), tirages indépendants | arbitraire ; l'indépendance est contredite | Aucun taux publié. Consigne « no releases were to be made on scene » à Toronto [IN6] ; ≈ 392 libérations individuelles à Oxford Circus, chiffre « unlikely to be accurate » selon le jugement [IN1]. L'acceptation dépend de l'objectif de la nasse et du calme à l'intérieur et à l'extérieur [IN1, IN6]. | Proche de 0 quand l'objectif est l'interpellation | La part libérée n'est pas un taux d'acceptation |
| Ouverture accordée | 80 s à 4 min, pour tous | contredit | Libérations filtrées et sélectives : groupes de dix avec fouille et photographie [IN1], paires de jeunes personnes [IN4], filtrage par groupes [FR4]. | Ouverture filtrée jusqu'à évacuation, avec suspensions | — |
| `relocating` | ouvertures de 80 s à 4 min, tournant toutes les 5 à 9 min | arbitraire | Aucun cas documenté. Le plus proche : libérations reprises puis suspendues à Oxford Circus [IN1]. | — | — |
| Réaction policière à la foule | aucune | contredit | Gaz, canons à eau et charges dans la nasse de la place d'Italie [FR4] ; grenades dans une nasse à Toulouse en 2019, sortie par une brèche lors d'un changement d'unités [FR16] ; cordon poussé de 20 à 30 m à Londres [IN2] ; libération suspendue après des poussées [IN1] ; au moins 61 blessés à New York [IN10]. La psychologie des foules associe l'action policière perçue comme indiscriminée à l'escalade du conflit [IN15, IN16]. | Admissible seulement comme scénario déclaré contrefactuel | Aucun seuil de déclenchement chiffré |

### Information et perception

| Paramètre | Valeur v2 | Statut | Appuis et contradictions | Plage fondée | Limites |
|---|---|---|---|---|---|
| Partage d'une ouverture visible | action 6 | arbitraire | Boucles Telegram de manifestants attestées en France [CO8] ; à Hong Kong, plus de la moitié des messages de chaînes principales portaient sur la situation sur place [CO9]. | — | Portée et réactivité des boucles françaises non publiées |
| Délai de diffusion | 30 s pour tous (`comm_delay=3`) | arbitraire | En stade plein, la latence des messages texte augmente nettement par rapport aux jours sans match [CO1]. Délai humain (voir, écrire, relayer, lire) mesuré nulle part. | Délai humain + délai réseau, tiré par groupe | — |
| Remise tout ou rien | un tirage par pas | inféré comme échec à l'émission | Un seul émetteur ; à la réception, les échecs dépendent de l'opérateur et du terminal [CO1]. | Séparer échec à l'émission et échec à la réception par groupe | — |
| Perte | 20 % (`comm_drop=0.2`) | inféré | Échec d'un message texte en stade plein : 0 à 30,8 % selon le réseau (prépublication non relue) [CO1]. Hors foule en France, SMS reçus en moins de 10 s avec un haut niveau de performance [CO2]. | 0 à 31 % pour un texte | Aucune mesure en manifestation française |
| Perte du profil difficile | 60 % | arbitraire (scénario de coupure) | Au-dessus des échecs texte mesurés ; proche d'un effondrement de réseau [CO4] ou d'une coupure [CO5]. Brouillage légal pour l'ordre public en France [CO7], jamais documenté en manifestation. | — | — |
| Indépendance des pertes | tirage à chaque pas | arbitraire | Les coupures documentées durent environ une heure [CO5] ou plus [CO6]. | État de coupure persistant | — |
| Validité d'un message | 3 min depuis l'envoi, même si la sortie s'est refermée (`message_ttl=18`) | arbitraire | Rumeurs fausses corrigées en 2 à 3 h pendant les émeutes anglaises de 2011 [CO11, CO12]. | Persistance de l'ordre de l'heure pour un message périmé | Aucune mesure sur un message tactique |
| Message unique | le plus récent remplace le précédent | arbitraire | Messages contradictoires difficiles à trier avant résolution [CO12]. | Plusieurs messages concurrents, dont une part fausse | — |
| Position exacte de la sortie | coordonnées exactes | arbitraire | Aucune source ; un message humain désigne une rue ou un carrefour. | Bruit de ± 1 case ou ± 1 tronçon | — |
| Vue | carré de ± 70 m sans occlusion (`obs_r=7`) | arbitraire | Aucune source pour 70 m. Façades continues le long des rues [CO17, PA12] : la vue hors de l'axe de la rue est bloquée. | Visibilité calculée sur les bâtiments OSM | Aucune étude de visibilité d'une ligne de police depuis une foule |
| Auto-localisation | parfaite | inféré | Erreur horizontale moyenne d'un smartphone en ville : 7 à 13 m [CO15]. | ± 1 case | Aucune mesure à Paris |
| Temps restant (`t/T` observé) | connu | contredit | Retenue « pour une durée non déterminée » [FR3] ; à Londres en 2009, une personne encerclée ne savait pas si elle resterait « vingt minutes ou douze heures » [IN3]. | Retirer de l'observation, ou bruiter | — |

### Sortie et récompense

Le modèle compte une réussite après 3 pas (30 s) hors du cordon, puis retire le groupe (`exit_env.py:279`). Les sorties documentées passent par un contrôle ou une interpellation : sortie une par une vers un contrôle d'identité puis la garde à vue (Paris, 2023) [FR7] ; relevé d'identité, palpation et photographies (Bellecour, 2010) [FR9] ; fouille, nom, adresse et photographie (Oxford Circus) [IN1] ; procédure ouverte contre 947 personnes (Francfort, 2013) [IN9] ; transfert des 542 personnes encerclées (Zurich, 2011) [IN11]. Des amendes de 135 € pour participation à un rassemblement interdit ont été invoquées à Paris en 2023 [FR13] ; aucune source ne documente une verbalisation systématique à la sortie d'une nasse parisienne.

| Paramètre | Valeur v2 | Statut | Appuis et contradictions | Plage fondée ou indication | Limites |
|---|---|---|---|---|---|
| Réussite | 30 s hors du cordon, puis retrait sans contrôle (`outside_hold=3`) | contredit | Voir le paragraphe ci-dessus. | Distinguer sortie libre, sortie contrôlée et interpellation | Aucune proportion française de personnes contrôlées ou interpellées parmi les encerclées |
| Sortie | +8 (`exit_reward`) | arbitraire (normatif) | « Sortir » recouvre des issues de valeurs différentes. | — | — |
| Sortie collective | +2 × fraction sortie au pas | arbitraire (normatif) | — | — | — |
| Coût de temps | 0,02 par pas | arbitraire (normatif) | Indemnités danoises fixées selon le temps passé assis dans le froid [IN12]. | Coût croissant sur un horizon en heures | — |
| Coût de densité | 0,25 × (n − 3) par groupe | arbitraire | Les sources décrivent des seuils et une durée d'exposition [PH4, PH6], pas une croissance quadratique. | Seuils vers 3 à 4 puis 5 à 6 p/m² | — |
| Tentative bloquée | 0,02 | arbitraire (normatif) | La conséquence réelle passe par la réaction policière [FR4, IN1]. | — | — |
| Demande | 0,002 | arbitraire | — | — | — |
| Groupe restant à la fin | −4 | arbitraire (normatif) | Les nasses réelles continuent au-delà de 24 min. | — | — |

## Cas chronométrés

| Cas | Pays, type de source | Observé → publié | Personnes | Durée | Première sortie | Sortie |
|---|---|---|---|---|---|---|
| Place d'Italie, Paris [FR4] | France, ONG (observation directe) | 16/11/2019 → 24/01/2020 | non chiffré | au moins 2 h selon un témoin [FR3] | nasse hermétique à 13 h 48 ; ouverture signalée à 15 h 49 | filtrée par groupes ; refus à l'approche des cordons |
| Pont de Sully, Paris [FR15] | France, ONG | 28/06/2019 → 2019 | ≈ 150 au début (estimation visuelle) | encerclement complet à 12 h 49 | sortie « négociée » à 13 h 57, 51 min après la dernière sommation | enlèvements un par un après les sommations |
| Nasses de rue, Paris [FR7] | France, témoignages recueillis par la presse | 03/2023 → 05/04/2023 | une vingtaine à plus de 100 | ≈ 1 h 30 alignés face au mur (un témoignage) | non précisée | une par une vers contrôle d'identité, fourgons, garde à vue |
| Place Bellecour, Lyon [FR8, FR9, FR10] | France, presse, témoignages, CEDH | 21/10/2010 → 2010 à 2024 | ≈ 600 | 13 h 30 à 19 h | non établie | sortie unique ; identité, palpation, photographies |
| Oxford Circus, Londres [IN1] | Royaume-Uni, constat judiciaire repris par la CEDH | 01/05/2001 → 15/03/2012 | jusqu'à 2 000 | ≈ 14 h à 21 h 45 | libérations individuelles l'après-midi ; libération finale à partir de 20 h 02 | groupes de dix ; fouille, nom, adresse, photographie |
| Bank et Climate Camp, Londres [IN2, IN3] | Royaume-Uni, judiciaire et inspection | 01/04/2009 → 2009 et 2011 | 4 000 à 5 000 (Bank) | plusieurs heures | en soirée | petits groupes ; cordon poussé de 20 à 30 m |
| Whitehall, Londres [IN4] | Royaume-Uni, judiciaire | 24/11/2010 → 09/2011 | ≈ 2 000 | plusieurs heures | « jeunes filles » autorisées à sortir à 13 h 25 | par paires, au compte-gouttes |
| Queen & Spadina, Toronto [IN6] | Canada, organisme de contrôle | 27/06/2010 → 05/2012 | ≈ 400 | ≈ 3 à 4 h | libération décidée vers 20 h 40, ≈ 2 h 50 après l'encerclement | arrestations une par une ; consigne de ne libérer personne sur place |
| Francfort [IN9] | Allemagne, ONG (observateurs présents) | 01/06/2013 → 02/2014 | ≈ 950 à 1 000 | 12 h 48 à 22 h 20 | appel à passer par un sas de contrôle à 13 h 23 ; point de passage annoncé à 16 h 02 | individuelle, identité relevée pour tous |
| Hambourg [IN8] | Allemagne, agence fédérale | 08/06/1986 → 2026 | 861 | jusqu'à 13 h | sortie contre relevé d'identité et arrestation provisoire | — |
| Mott Haven, New York [IN10] | États-Unis, ONG | 04/06/2020 → 30/09/2020 | ≈ 300 | plusieurs heures | aucune | au moins 263 arrestations |
| Zurich [IN11] | Suisse, judiciaire | 01/05/2011 → 2016 | 542 | plusieurs heures | aucune sur place | transfert de tous |
| Copenhague [IN12] | Danemark, presse | 12/2009 → 25/01/2012 | ≈ 900 à 1 000 | plus de 3 h | aucune | arrestation administrative |

## Désaccords entre sources

| Sujet | Versions | Usage proposé |
|---|---|---|
| Oxford Circus : libérations individuelles | ≈ 392, chiffre jugé peu fiable ; « plus près de 400 que de 200 » selon le juge [IN1] | Ordre de grandeur seulement |
| Oxford Circus : effectif | jusqu'à 2 000 dedans selon l'arrêt [IN1] ; 3 000 selon un texte universitaire [FR13] | Retenir l'arrêt |
| Toronto : durée | « quatre heures » ; ≈ 2 h 50 entre confinement et ordre de libération [IN6] | Distinguer décision et libération effective |
| Concorde, mars 2023 | 292 interpellations le 16/03 [FR5] ; même chiffre daté du 18/03 [FR14] ; 256 gardes à vue le 16/03 [FR6] | Deux mesures distinctes, aucune ne compte des personnes encerclées |
| Pont de Sully | discussion de « deux heures » selon le ministre ; 51 min entre la dernière sommation et la sortie selon l'observatoire [FR15] | Garder les deux versions |
| Hambourg | 861 [IN8] ; 838 gardes à vue et 22 arrestations [IN20] | Plage 838 à 861 |
| Itaewon, 29/10/2022 | 8 à 11 personnes par m² ou par yard² selon les médias [PH13] | Vérifier l'unité dans le rapport officiel |
| Capacité des goulots | facteur 2 entre guides, facteur 4 entre guides et mesures à 1 m [PH3] | Donner une plage |
| Mobilité à 2 ou 3 p/m² | « on ne peut plus bouger » selon un policier [PA7] ; 0,4 à 0,6 m/s selon Weidmann [PH2] | Garder Weidmann pour la vitesse |

## Ce qu'aucune source ne fournit

- un taux d'acceptation d'une demande de sortie, ou un délai de réponse ;
- la largeur d'un point de sortie, ou un débit mesuré à travers une brèche de cordon sans contrôle ;
- une densité mesurée dans une nasse parisienne, ou un comptage indépendant des personnes encerclées ;
- le délai humain entre l'observation d'une sortie et sa réception par la foule ;
- la fréquence des différents types de nasse à Paris.

Ces valeurs relèvent d'un travail de terrain (observateurs, entretiens, mesures radio et sonores) ou de journaux de commandement non publics. D'ici là, elles doivent être balayées et présentées comme non calibrées.

## Mécanismes absents

Classés selon l'effet attendu sur la part de la foule sortie et sur l'exposition à la densité. Ce classement est un raisonnement d'ordre de grandeur, pas un résultat de simulation. Chaque mécanisme demande sa propre issue avant toute modification du modèle.

1. Délais de décision et horizon en heures. Avec les délais documentés et 24 min, `delayed`, `responsive` et `relocating` se ramènent à `sealed`.
2. Débit de sortie fixé par la procédure (temps de traitement par personne, nombre de files). Il divise le flux de sortie par 50 à 600 et rend l'effectif de la foule déterminant.
3. Issue de la sortie : libération, contrôle, amende, interpellation, transfert, nouvelle nasse ; objectif déclaré de la nasse.
4. Réaction policière dépendante de l'état de la foule et de l'extérieur du cordon.
5. Physique de densité : vitesse selon Weidmann, capacité des faces de case, plafond d'occupation.
6. Foule hétérogène et divisible ; libération prioritaire de catégories (personnes vulnérables, presse, passants).
7. Géométrie du cordon propre à chaque lieu, placée aux débouchés.
8. Perception et information : occlusion, coupures persistantes, échecs par récepteur, relais oral de proche en proche, messages faux ou périmés, retrait du temps restant.
9. Inconfort cumulé (froid, eau, toilettes), sensible seulement sur un horizon en heures.
10. Largeur d'ouverture paramétrable et contre-flux.

## Fiches sources

Les fiches détaillées sont dans [docs/sources/](sources/), une par source, au format YAML, avec le modèle et les règles de rédaction. Cinq sont remplies, pour des sources françaises : FR2, FR3, FR4, FR7, FR15. Chaque fiche donne les passages exacts, la conversion dans l'échelle du modèle, les paramètres concernés et ce que la source ne permet pas de conclure.

## Sources à vérifier

Ces sources n'ont été vues que dans des extraits de moteur de recherche ou sont inaccessibles. Leurs chiffres ne sont pas repris ci-dessus.

- Europe 1, plainte relative à la place d'Italie : effectif et durée selon les plaignants ([lien](https://www.europe1.fr/node/759306)).
- LyonMag, arrêt de la CEDH sur Bellecour : nombre de personnes contrôlées ([lien](https://www.lyonmag.com/article/134500/garde-a-vue-a-ciel-ouvert-place-bellecour-la-france-condamnee-par-la-cour-europeenne-des-droits-de-l-homme)).
- Swissinfo, arrêt de la CEDH du 19/12/2023 sur l'encerclement de Zurich ([lien](https://swissinfo.ch/eng/politics/strasbourg-court-criticises-switzerland-for-police-demo-tactics/49073874)).
- BRF, Francfort 2013 : effectif et durée ([lien](https://brf.be/gesellschaft/landundleute/767746)).
- Opensignal, réseaux mobiles pendant la cérémonie d'ouverture des JO de 2024 ([lien](https://insights.opensignal.com/2024/08/06/ensuring-seamless-user-experience-how-mobile-networks-held-up-during-the-paris-olympics-opening)).
- McPhail, entretien dans *Contexts* (2008), et résumé de Templeton, Drury et Philippides (2018) sur la composition des foules.
- SDIS 86, mémento ERP : densité réglementaire du public debout (lien mort au 25/10/2026).

## Registre des sources

« Lecture » : lu (texte consulté), résumé (résumé seul), secondaire (connu par une autre source), mesure locale (calcul fait pour cette fiche, non publié dans le dépôt). Les sources qui ont une fiche dans [docs/sources/](sources/) sont listées dans la section précédente. Liens vérifiés le 25/10/2026 ; quelques sites refusent les requêtes automatiques.

| ID | Source | Pays | Type | Observé | Publié | Lecture |
|---|---|---|---|---|---|---|
| PH1 | [Weidmann, *Transporttechnik der Fussgänger*, IVT ETH Zürich](https://research-collection.ethz.ch/handle/20.500.11850/242008) | Suisse (données multinationales) | synthèse académique | — | 1993 | secondaire |
| PH2 | [Kretz, arXiv:1512.01426](https://arxiv.org/abs/1512.01426) | — | académique | — | 2015 | lu |
| PH3 | [Seyfried et al., arXiv:physics/0702004](https://arxiv.org/abs/physics/0702004) | Allemagne | expérience et comparaison de guides | — | 2009 | lu |
| PH4 | [Still, thèse, chapitre 3](https://gkstill.com/CV/PhD/Chapter3.html) | Royaume-Uni | thèse (Green Guide) | — | 2000, mise à jour 2017 | lu |
| PH5 | [Fruin, « The Causes and Prevention of Crowd Disasters »](https://gkstill.com/Support/crowd-flow/fruin/Fruin2.html) | États-Unis | communication | — | 1993 | lu |
| PH6 | [Helbing, Johansson & Al-Abideen, arXiv:physics/0701203](https://arxiv.org/abs/physics/0701203) | Arabie saoudite | analyse vidéo | 12/01/2006 | 2007 | lu |
| PH7 | [Helbing & Mukerji, arXiv:1206.5856](https://arxiv.org/abs/1206.5856) | Allemagne | analyse a posteriori | 24/07/2010 | 2012 | lu |
| PH10 | [Thunderhead, Pathfinder, mode SFPE](https://www.thunderheadeng.com/docs/2026-1/pathfinder/appendices/technical-reference/sfpe) ; [cas IMO](https://www.thunderheadeng.com/docs/2025-1/pathfinder/appendices/verification-validation/imo/imo-11) | États-Unis | documentation logicielle | — | 2025-2026 | lu |
| PH11 | [Burstedde et al., arXiv:cond-mat/0102397](https://arxiv.org/abs/cond-mat/0102397) | Allemagne | modèle | — | 2001 | lu |
| PH12 | [Hänseler et al., PedCTM, HKSTS 2014](https://infoscience.epfl.ch/record/204602/files/Flurin_HKSTS_2014.pdf) | Suisse | modèle | — | 2014 | lu |
| PH13 | [AP via Khaosod English](https://www.khaosodenglish.com/news/international/2023/01/13/s-korean-police-seek-manslaughter-charges-over-deadly-crush/) ; [CBS News](https://www.cbsnews.com/news/halloween-crowd-surge-seoul-manslaughter-charges-sought-almost-160-dead) | Corée du Sud | presse | 29/10/2022 | 2023 | lu ; unité contestée |
| PH14 | [Ministère de la Culture, fiche n° 11, effectifs et issues de secours](https://www.culture.gouv.fr/fr/Media/Thematiques/Securite-Surete/Fichiers/securite-incendie/fiche-n-11-effectifs-et-issus-de-secours-2022) | France | réglementaire (ERP) | — | 2022 | lu |
| PH15 | [Bosina & Weidmann, STRC 2018](https://www.strc.ch/2018/Bosina_Weidmann.pdf) | Suisse | académique | — | 2018 | lu |
| PA1 | [Wikipédia, listes des voies par arrondissement](https://fr.wikipedia.org/wiki/Liste_des_voies_du_11e_arrondissement_de_Paris) (1er-6e, 10e-13e) | France | mesure locale sur les infobox | état au 25/10/2026 | — | mesure locale |
| PA2 | Wikipédia : [République](https://fr.wikipedia.org/wiki/Place_de_la_R%C3%A9publique_%28Paris%29), [Bastille](https://fr.wikipedia.org/wiki/Place_de_la_Bastille), [Nation](https://en.wikipedia.org/wiki/Place_de_la_Nation), [Italie](https://fr.wikipedia.org/wiki/Place_d%27Italie_%28Paris%29) | France | encyclopédie | — | consulté le 25/10/2026 | lu |
| PA3 | Rastérisation des lieux de test avec `osm_map.py` | France | mesure locale | 25/10/2026 | — | mesure locale |
| PA4 | [Rolland, « Le comptage des manifestants », *Tangente* n° 198](https://eric.univ-lyon2.fr/arolland/publis/TANGENTE198_PREPRINT.pdf) | France | universitaire | — | 2021 | lu |
| PA5 | [Reporterre](https://reporterre.net/350-interpellations-arbitraires-place-de-la-Republique-salaries-etudiants) | France | presse | 29/11/2015 | 2015 | lu |
| PA6 | [Public Sénat, 1er mai 2018](https://www.publicsenat.fr/actualites/politique/1er-mai-en-france-heurts-a-paris-apres-des-manifestations-calmes-en-regions-85521) | France | presse reprenant la préfecture | 01/05/2018 | 01/05/2018 | lu |
| PA7 | [INA, comptage des manifestants](https://www.ina.fr/ina-eclaire-actu/manifestation-greve-retraite-comptage-manifestants) | France | presse d'archives | 1998 et 2023 | 2023 | lu |
| PA8 | [Wahlström & Wennerhag, « Alone in the crowd », *International Sociology* 29(6)](https://gup.ub.gu.se/file/143020) | 8 pays d'Europe de l'Ouest | enquête académique | 2009-2012 | 2014 | lu |
| PA9 | [Nicolas & Hassan, arXiv:2107.13293](https://arxiv.org/abs/2107.13293) | plusieurs pays | revue académique | — | 2021 | lu |
| PA12 | [Wikipédia, règlements d'urbanisme de Paris](https://fr.wikipedia.org/wiki/R%C3%A8glements_d%27urbanisme_de_Paris) | France | encyclopédie | 1783-1902 | consulté le 25/10/2026 | lu |
| FR1 | [Conseil d'État, 10/06/2021, n° 444849](https://www.conseil-etat.fr/fr/arianeweb/CE/decision/2021-06-10/444849) | France | juridiction | SNMO 2020 | 10/06/2021 | lu |
| FR2 | [Conseil d'État, 29/12/2023, n° 461513](https://www.conseil-etat.fr/fr/arianeweb/CE/decision/2023-12-29/461513) | France | juridiction | SNMO 2021 | 29/12/2023 | lu |
| FR3 | [Assemblée nationale, rapport n° 3786](https://www.assemblee-nationale.fr/dyn/15/rapports/ceordre/l15b3786_rapport-enquete.pdf) | France | rapport parlementaire | auditions de 2020 | 20/01/2021 | lu |
| FR4 | [Observatoire parisien des libertés publiques, place d'Italie](https://www.ldh-france.org/wp-content/uploads/2019/04/Rapport-16-novembre-2019-La-strate%CC%81gie-de-la-nasse-contre-le-droit-de-manifester.pdf) | France | ONG, observation directe | 16/11/2019 | 24/01/2020 | lu |
| FR5 | [Préfecture de police, arrêté n° 2023-00311](https://www.ldh-france.org/wp-content/uploads/2023/05/Arrete-n%C2%B02023-00311-du-22-mars-2023.pdf) | France | officiel | 16-21/03/2023 | 22/03/2023 | lu |
| FR6 | [Assemblée nationale, commission d'enquête 2023, compte rendu n° 10](https://questions.assemblee-nationale.fr/dyn/16/comptes-rendus/cegrvimani/l16cegrvimani2223010_compte-rendu.pdf) | France | audition du parquet | 16/03-03/05/2023 | 19/06/2023 | lu |
| FR7 | [Politis, témoignages](https://www.politis.fr/articles/2023/04/coups-insultes-brimades-les-victimes-temoignent/) | France | presse recueillant des témoignages | 16-20/03/2023 | 05/04/2023 | lu |
| FR8 | [Lyon Capitale](https://www.lyoncapitale.fr/?p=69227) | France | presse | 21/10/2010 | 26/11/2010 | lu |
| FR9 | [Reporterre, récit d'étudiants](https://reporterre.net/Quand-la-police-de-M-Sarkozy) | France | témoignage collectif | 21/10/2010 | 25/10/2010 | lu |
| FR10 | [Strasbourg Observers, commentaire de l'arrêt CEDH du 08/02/2024 sur Bellecour](https://strasbourgobservers.com/2024/05/31/triumph-or-pyrrhic-victory-for-the-freedom-to-protest-a-critical-discussion-of-auray-v-france/) | France (CEDH) | commentaire universitaire | 21/10/2010 | 31/05/2024 | lu |
| FR13 | [Fillieule, « Un maintien de l'ordre à la dérive », *Possibles*](https://france.attac.org/IMG/pdf/fillieule-possibles.pdf) | France | universitaire | 2010-2023 | 2023 | lu |
| FR14 | [Question écrite AN n° 6780](https://questions.assemblee-nationale.fr/dyn/16/questions/QANR5L16QE6780.pdf) | France | parlementaire | 03/2023 | 28/03/2023 | lu |
| FR15 | [Observatoire parisien, pont de Sully](https://www.ldh-france.org/wp-content/uploads/2019/04/Obs-Paris-Rapport-d%C3%A9fenseur-des-droits-Manif-pont-de-Sully.pdf) | France | ONG | 28/06/2019 | 2019 | lu |
| FR16 | [Observatoire toulousain des pratiques policières, « 4 ans d'observations »](https://www.ldh-france.org/wp-content/uploads/2021/04/rapport-toulouse-4-ans-dobservations-final-compresse.pdf) | France | ONG | 13/04/2019 | 04/2021 | lu |
| FR17 | [Sénat, « L'avenir du maintien de l'ordre », synthèse](https://www.senat.fr/rap/r26-078/r26-078-syn.pdf) | France | parlementaire | — | 10/2026 | lu |
| FR19 | [Audition du préfet de police](https://www.assemblee-nationale.fr/dyn/15/comptes-rendus/ceordre/l15ceordre2021043_compte-rendu.pdf) | France | audition | — | 02/12/2020 | lu |
| FR21 | [Table ronde des syndicats de journalistes](https://www.assemblee-nationale.fr/dyn/15/comptes-rendus/ceordre/l15ceordre2021021_compte-rendu.pdf) | France | audition | — | 05/11/2020 | lu |
| IN1 | [CEDH, Grande Chambre, 15/03/2012, req. n° 39692/09 et autres](https://www.statewatch.org/media/documents/news/2012/mar/echr-kettling-judgment.pdf) | Royaume-Uni (CEDH) | constat judiciaire | 01/05/2001 | 15/03/2012 | lu |
| IN2 | [[2011] EWHC 957 (Admin)](https://caselaw.nationalarchives.gov.uk/ewhc/admin/2011/957) | Royaume-Uni | judiciaire | 01/04/2009 | 14/04/2011 | lu |
| IN3 | [HMIC, *Adapting to Protest*](https://www.rightofassembly.info/assets/downloads/HIMC_Adapting_to_protest_%282009%29.pdf) | Royaume-Uni | inspection | 01/04/2009 | 07/2009 | lu |
| IN4 | [[2011] EWHC 2317 (Admin)](https://caselaw.nationalarchives.gov.uk/ewhc/admin/2011/2317) | Royaume-Uni | judiciaire | 24/11/2010 | 09/2011 | lu |
| IN6 | [OIPRD, *Policing the Right to Protest*](https://leca.ca/wp-content/uploads/G20-Systemic-Review-2012_E.pdf) | Canada | organisme de contrôle | 26-27/06/2010 | 05/2012 | lu |
| IN8 | [bpb, *Hamburger Kessel*](https://www.bpb.de/kurz-knapp/taegliche-dosis-politik/578611/hamburger-kessel/) | Allemagne | agence fédérale | 08/06/1986 | 2026 | lu |
| IN9 | [Komitee für Grundrechte und Demokratie, Blockupy](https://www.grundrechtekomitee.de/fileadmin/user_upload/drupal_import_DEPRECATED/sites/default/files/Blockupy-Brosch%C3%BCre_I_0.pdf) | Allemagne | ONG, observateurs présents | 01/06/2013 | 02/2014 | lu |
| IN10 | [Human Rights Watch, *Kettling Protesters in the Bronx*](https://www.hrw.org/report/2020/09/30/kettling-protesters-bronx/systemic-police-brutality-and-its-costs-united-states) | États-Unis | ONG | 04/06/2020 | 30/09/2020 | lu |
| IN11 | [Tribunal fédéral, ATF 142 I 121](https://servat.unibe.ch/dfr/bge/c1142121.html) | Suisse | judiciaire | 01/05/2011 | 2016 | lu |
| IN12 | [CPH Post](https://cphpost.dk/2012-01-25/general/climate-conference-mass-arrests-illegal-court-upholds/) | Danemark | presse | 12/2009 | 25/01/2012 | lu |
| IN15 | [Reicher, Stott, Cronin & Adang, résumé NCJRS](https://www.ojp.gov/ncjrs/virtual-library/abstracts/integrated-approach-crowd-psychology-and-public-order-policing) | Royaume-Uni | académique | — | 2004 | résumé |
| IN16 | [Stott & Reicher, *European Journal of Social Psychology*](https://research-portal.st-andrews.ac.uk/en/publications/crowd-action-as-intergroup-process-introducing-the-police-perspec/) | Royaume-Uni | académique | 1990 | 1998 | résumé |
| IN20 | [dewiki, *Hamburger Kessel*](https://dewiki.de/Lexikon/Hamburger_Kessel) | Allemagne | encyclopédie | 08/06/1986 | — | lu |
| CO1 | [Nabil et al., arXiv 2607.16008](https://arxiv.org/html/2607.16008v1) | États-Unis | prépublication non relue | 2025 | 07/2026 | lu |
| CO2 | [Arcep, qualité des services mobiles](https://www.arcep.fr/actualites/actualites-et-communiques/detail/n/qualite-des-services-mobiles-en-metropole-260625.html) | France | régulateur, hors foule | 2024 | 26/06/2025 | lu |
| CO4 | [The Local, Duisbourg](https://www.thelocal.com/20110516/35043) | Allemagne | presse | 24/07/2010 | 16/05/2011 | lu |
| CO5 | [NetBlocks, Biélorussie](https://netblocks.org/reports/internet-disruption-hits-belarus-on-election-day-YAE2jKB3) | Biélorussie | mesure technique | 08/2020 | 2020 | lu |
| CO6 | [Fierce Network, BART](https://www.fierce-network.com/wireless/fcc-to-investigate-san-francisco-bart-s-wireless-service-shutdown) | États-Unis | presse | 11/08/2011 | 2011 | lu |
| CO7 | [Arcep, brouilleurs (art. L.33-3-1 CPCE)](https://www.arcep.fr/la-regulation/grands-dossiers-reseaux-mobiles/les-brouilleurs-et-repeteurs-de-reseaux-mobiles.html) | France | juridique | — | 2020 | lu |
| CO8 | [Next, boucles Telegram](https://next.ink/5262/la-police-en-dit-plus-sur-surveillance-manifestants-et-boucles-telegram/) | France | presse rapportant une audition | 21/10/2020 | 2021 | lu |
| CO9 | [Urman, Ho & Katz, *PLOS ONE*](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0256675) | Hong Kong | académique | 2019-2020 | 2021 | lu |
| CO11 | [Poynter, analyse du Guardian](https://www.poynter.org/news/guardian-twitter-dispels-rumors-just-well-it-propagates-them) | Royaume-Uni | presse | 08/2011 | 12/2011 | lu |
| CO12 | [Zubiaga et al., *PLOS ONE*](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4778911/) | international | académique | — | 2016 | lu |
| CO15 | [Merry & Bettinger, *PLOS ONE*](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6638960/) | États-Unis | académique | — | 2019 | lu |
| CO17 | [Ville de Paris, Haussmann](https://www.paris.fr/pages/haussmann-l-homme-qui-a-transforme-paris-23091) | France | institutionnel | — | — | lu |
