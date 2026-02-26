# Évaluation de modules de localisation pour véhicule autonome (ROS 2 / CARLA)

Ce dépôt contient **le rapport de projet** : *Évaluation de modules de localisation pour véhicule autonome — ROS 2 & CARLA : robot_localization (EKF/UKF)*.  
Le travail met en place un protocole d’évaluation pour comparer des configurations de **fusion multi-capteurs** (odométrie, IMU, GNSS) avec le package **`robot_localization`** (filtres **EKF** et **UKF**) dans **CARLA**, en comparant l’estimation à la **vérité terrain** (ground truth).

> 📦 Contenu du dépôt : uniquement ce `README.md` + le PDF du rapport.

---

## Contexte & objectifs

- Construire une chaîne reproductible sous **ROS 2 + CARLA** pour évaluer des modules de localisation.
- Comparer plusieurs **configurations capteurs** (odom, odom+IMU, odom+IMU+GNSS).
- Étudier l’impact des réglages d’incertitudes (**Q**, **P0**) et comparer **EKF vs UKF**.
- Mesurer la performance via des métriques position/orientation (RMSE, erreur 2D, erreur yaw).

---

## Stack technique

- **ROS 2**
- **CARLA**
- **robot_localization** : `ekf_node`, `ukf_node`
- Capteurs simulés : **Odométrie**, **IMU**, **GNSS**
- Vérité terrain CARLA (odometry GT)

---

## Données et signaux évalués (résumé)

- **GT (ground truth)** : odométrie du véhicule dans CARLA
- **IMU** : vitesse angulaire / orientation (selon config)
- **GNSS** : position globale (selon config)
- **Sortie filtre** : odométrie filtrée (`/odometry/filtered`)

Synchronisation basée sur le temps simulé (`/clock`) et comparaison **GT vs estimation filtrée**.

---

## Métriques

- Erreurs position : \(e_x\), \(e_y\), et erreur 2D \(e_{2D} = \sqrt{e_x^2 + e_y^2}\)
- Erreur yaw : \(e_\psi = wrap(\psi_{est} - \psi_{gt})\)
- Synthèse : **RMSEx**, **RMSEy**, **RMSE2D**, **RMSEyaw**
- Visualisations : trajectoires 2D, courbes d’erreurs, histogrammes

---

## Résultats (extraits)

### EKF — comparaison des configurations capteurs

| Run | Capteurs | RMSE2D (m) | RMSEyaw (rad) |
|---|---|---:|---:|
| RUN1 | Odom | 0.181 | 0.036 |
| RUN2 | Odom + IMU | 0.173 | 0.151 |
| RUN3 | Odom + IMU + GNSS | 0.172 | 0.153 |

**Observation :** sur cette séquence, la position discrimine peu (l’odométrie est déjà proche de la GT), alors que le **yaw** est plus sensible au modèle de fusion et aux réglages IMU.

### UKF — impact des choix IMU

| Run | Configuration IMU | RMSE2D (m) | RMSEyaw (rad) |
|---|---|---:|---:|
| RUN5 | gyro-only | 0.184 | 0.097 |
| RUN6 | yaw + gyro | 0.199 | 0.0275 |

**Observation :** l’UKF peut fortement **améliorer le yaw** (RUN6), avec un compromis possible sur l’erreur position.

---

## Points techniques notables

- Importance du paramétrage `robot_localization` (frames, signaux fusionnés, `two_d_mode`, etc.).
- Analyse de l’impact des matrices d’incertitudes :
  - `process_noise_covariance` (**Q**)
  - `initial_estimate_covariance` (**P0**)
- **Attention UKF :** certaines covariances issues de la simulation peuvent être nulles et rendre le filtre instable (gestion discutée dans le rapport).

---

## Auteurs & encadrement

- **Sekou DAOU**
- **Touwende OUEDRAOGO**

Encadrement :
- Zaynab EL MAWAS
- Maan EL BADAOUI EL NAJJAR

---

## Citation

Si vous réutilisez ce travail (rapport, figures, résultats), merci de citer :

> DAOU S., OUEDRAOGO T., *Évaluation de modules de localisation pour véhicule autonome — ROS 2 & CARLA : robot_localization (EKF/UKF)*, Université de Lille.

---

## Licence

À définir par l’auteur (ex. MIT / Apache-2.0).  
Par défaut, **tous droits réservés** tant qu’aucune licence n’est ajoutée.
