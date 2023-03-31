---
{"dg-home":false,"dg-publish":true,"permalink":"/app-coach/mission-1-interface-graphique/","dgPassFrontmatter":true}
---

- [[Accueil\|Accueil]]
# Présentation du projet

AppCoach est une application Android qui a pour but de calculer l'IMG (Indice de Masse Grasse) et indique notre corpulence (trop maigre, parfait, surpoids).

# Support

L'application utilise Xamarin sur Visual studio code 2019 codé en C#.

# Interface graphique

La création de l'interface se code en **XML**. via le fichier *activity_main.xml*.
Celui-ci est composé de plusieurs nœuds intitulés *LinearLayout* permettant de créé des cases afin d'y insérer les divers éléments dont nous avons besoin pour la construction de l'interface. 
Par exemple, le code suivant permet de créé le *LinearLayout* composer des radios boutons :

```xml
<LinearLayout
                android:orientation="vertical"
                android:minWidth="50px"
                android:minHeight="15px"
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:id="@+id/linearLayout5"
                android:layout_marginRight="0.0dp"
                android:layout_weight="1"
                android:layout_gravity="center_vertical|center_horizontal">
	<RadioGroup
                    android:minWidth="25px"
                    android:minHeight="25px"
                    android:layout_width="wrap_content"
                    android:layout_height="match_parent"
                    android:layout_gravity="center_horizontal"
                    android:id="@+id/radioGroup1"
                    android:gravity="center">
                    <RadioButton
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Femme"
                        android:layout_gravity="center_horizontal|left"
                        android:id="@+id/rdFemme"
                        android:textSize="22sp"
                        android:checked="true" />
                    <RadioButton
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Homme"
                        android:layout_gravity="center_horizontal|left"
                        android:id="@+id/rdHomme"
                        android:textSize="22sp" />
                </RadioGroup>
	 </LinearLayout>
```

```xml
<LinearLayout
	android:layout_weight="1"/>
```
> Indique la place qu'il prend par rapport aux autres *LinearLayout* dans le *LinearLayout* principal.

```xml
<LinearLayout
	android:layout_gravity="center_vertical|center_horizontal"/>
```
>Permets de centrer le radio bouton dans le *LinearLayout* de droite.

- Ce code permet d'obtenir ce qu'il y a dans le rectangle bleu ci-dessous : 
![AppCoach_accueil.png](/img/user/AppCoach/Images/AppCoach_accueil.png)

# Modèle MVC 

Le code est fait sous la forme d'un MVC (Modèle Vue Contrôleur).

### Le modèle  
> Contient l'accès aux données, ici nous avions la classe **Profil** qui initialise les variables pour créer le profil de l'utilisateur (le poids, la taille, l'âge et le sexe).

La classe est composer du constructeur : 
```C#
		private int sexe;
        private int poids;
        private int taille;
        private int age;
        private double img;
        private string message;
        
public profil(int unSexe, int unPoids, int uneTaille, int unAge)
        {
            sexe = unSexe;
            poids = unPoids;
            taille = uneTaille;
            age = unAge;
            img = calculImg();
            message = resultatImg();
        }
```

Ainsi que de tous les getters pour accéder aux données de la classe : 
```C#
public int GetTaille()
        {
            return taille;
        }
```

### La vue 
>Permets d'afficher les données en initialisant des liens avec les objets de l'interface.

```C#
			txtPoids = (EditText)FindViewById(Resource.Id.txtPoids);
            txtTaille = (EditText)FindViewById(Resource.Id.txtTaille);
            txtAge = (EditText)FindViewById(Resource.Id.txtAge);
            rdHomme = (RadioButton)FindViewById(Resource.Id.rdHomme);
            rdFemme = (RadioButton)FindViewById(Resource.Id.rdFemme);
            lbIMG = (TextView)FindViewById(Resource.Id.lbIMG);
            imgSmiley = (ImageView)FindViewById(Resource.Id.imgSmiley);
            btCalculer = (Button)FindViewById(Resource.Id.btCalculer);
```

### Le contrôleur 
> Chargé de la logistique applicative, c'est à dire de récupérer les données saisies, appels les fonctions, traite les données et appel la vue pour l'affichage.

Création du profil avec les données saisies par l'utilisateur :
```C#
public void CreerProfil(int unPoids, int uneTaille, int unAge, int unSexe)
        {
            profil = new profil(unSexe, unPoids, uneTaille, unAge);
        }
```

# Les fonctions 

Pour le fonctionnement de l'application nous avons besoin de diverses fonctions. 

### Fonctions du modèle

>Les fonctions du modèle permettent de mémoriser les données de la classe.

Le calcul de l'IMG (le poids en Kg et la taille en mètres) : 
```C#
public double calculImg()
        {
            double laTaille = (double)(taille) / 100;
            return (double) ((1.2 * poids / (laTaille * laTaille)) + (0.23 * age) -                                              |           (10.83 * sexe) - 5.4);
        }
```

Ainsi qu'une fonction permettant d'interpréter le résultat obtenu :
```C#
public string resultatImg()
        {
            string message = "";
            if(sexe==0)
            {
                if (calculImg() < 15)
                {
                    message = " Trop maigre ! ";
                }
                else if (calculImg() > 30)
                {
                    message = " Surpoids ! ";
                }
                else
                {
                    message = " Parfait ! ";
                }
                     
            }
            else if (sexe == 1)
            {
                if (calculImg() < 10)
                {
                    message = " Trop maigre ! ";
                }
                else if (calculImg() > 25)
                {
                    message = " Surpoids ! ";
                }
                else
                {
                    message = " Parfait ! ";
                }

            }

            return message;
        }
```

### Fonctions de la vue 

>Les fonctions de la vue servent à la gestion des évènements.

Vérifications que tous les champs soient remplis et correctement pour éviter les erreurs : 
```C#
private void btCalculer_Click(object sneder, EventArgs e)
        {
            int poids = 0;
            int taille = 0;
            int age = 0;
            int sexe = 0;

            try
            {
                poids = int.Parse(txtPoids.Text);
                taille = int.Parse(txtTaille.Text);
                age = int.Parse(txtAge.Text);

            }catch (Exception ex)
            {
                Toast.MakeText(this, "Saisie incorrecte ", ToastLength.Long).Show();
            }

            if (rdHomme.Checked)
            {
                sexe = 1;
            }

            //Controle des saisies
            if (poids == 0  ||  taille ==0 ||age == 0 )
            {
                Toast.MakeText(this, "Merci de remplir tout les champs", ToastLength.Long).Show();
            }
            else
            {
                AfficherResultat(poids, taille, age, sexe);
            }
        }
```

- Elle affiche des MessageBox permettant à l'utilisateur de pouvoir apporter une correction :
![AppCoach_saisie_incorrecte.png](/img/user/AppCoach/Images/AppCoach_saisie_incorrecte.png)![AppCoach_remplir_champs.png](/img/user/AppCoach/Images/AppCoach_remplir_champs.png)

Affichage de l'image et de la couleur du texte en fonction du résultat obtenu : 
```C#
private void AfficherResultat(int unPoids, int uneTaille, int unAge, int unSexe)
        {
            //Création du profil et récupération des informations
            this.controle.CreerProfil(unPoids, uneTaille, unAge, unSexe);
            double img = this.controle.GetIMG();
            string message = this.controle.GetMessage();
            if (message == " Parfait ! ")
            {
                imgSmiley.SetImageResource(Resource.Drawable.Smiley_Ok);
                lbIMG.SetTextColor(Android.Graphics.Color.DarkGreen);
            }
            else if (message == " Trop maigre ! ")
            {
                imgSmiley.SetImageResource(Resource.Drawable.Smiley_PasTop);
                lbIMG.SetTextColor(Android.Graphics.Color.DarkRed);
            }
            else
            {
                imgSmiley.SetImageResource(Resource.Drawable.Smiley_No);
                lbIMG.SetTextColor(Android.Graphics.Color.DarkRed);
            }
            lbIMG.Text = String.Format("{0:0.00}", img) + "% " + message;
        }
```

- Interprétation du résultat par les smileys et la couleur du texte
![AppCoach_img_maigre.png](/img/user/AppCoach/Images/AppCoach_img_maigre.png)
![AppCoach_img_parfait.png](/img/user/AppCoach/Images/AppCoach_img_parfait.png)
![AppCoach_img_surpoids.png](/img/user/AppCoach/Images/AppCoach_img_surpoids.png)

# Tests Unitaires

>Les tests Unitaires servent à vérifier le bon fonctionnement de fonctions précises du programme.

Ici, nous avons effectué des tests unitaires sur les fonctions du calcul de l'IMG ainsi que sur l'interprétation du calcul de l'IMG. Pour cela nous avons donné différentes données avec les résultats qu'ils doivent retourner. 

Test du calcul de l'IMG sur un profil :
```C#
public void TestUnitaireImg()
        {
            //Arrange​
            profil unProfil;
            unProfil = new profil(0, 47, 158, 19);

            double resultatAttendu = 21.5;

            //Act
            double resultatTest = unProfil.calculImg();

            //Assert
            Assert.AreEqual(resultatAttendu, resultatTest, delta: 0.1);

        }
```
- La section *Arrange* initialise et définit les valeurs des données transmises à la méthode testée.
- La section *Act* appel la méthode à tester avec les valeurs en paramètre.
- La section *Assert* vérifie que le résultat obtenu par la méthode soit conforme au résultat attendu avec une différence possible à 0,1.

Test sur l'interprétation de tous les types de profils possibles : 
```C#
//Femme trop maigre
            //Arrange​
            profil unProfil3;
            unProfil3 = new profil(0, 20, 158, 19);

            string resultatAttendu3 = " Trop maigre ! ";

            //Act
            string resultatTest3 = unProfil3.resultatImg();

            //Assert
            Assert.AreEqual(resultatAttendu3, resultatTest3);

//Homme parfait
            //Arrange​
            profil unProfil4;
            unProfil4 = new profil(1, 62, 177, 28);

            string resultatAttendu4 = " Parfait ! ";

            //Act
            string resultatTest4 = unProfil4.resultatImg();

            //Assert
            Assert.AreEqual(resultatAttendu4, resultatTest4);
```