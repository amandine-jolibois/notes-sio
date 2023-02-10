---
{"dg-home":false,"dg-publish":true,"permalink":"/app-coach/mission-2/","dgPassFrontmatter":true}
---

- [[Accueil\|Accueil]]
# Présentation de la mission 

Pour cette mission nous devions **Sérialiser** les données de l'application. La sérialisation est un processus de conversion d’un objet en flux d’octets pour stocker l’objet ou le transmettre à la mémoire (l'enregistrement des données).
Puis nous devions Désérialiser les données. La désérialisation consiste à charger les données qui ont été sérialiser.

# Classe Sérialiser

Pour sérialiser nos données nous devions crées une *classe abstraite Sérialiser* dans un dossier *outils*. 

>Une classe abstraite est une classe qui n'est pas instanciable c'est à dire utiliser un *new* pour créer un nouvel objet.

- Méthode pour la sérialisation : 
```C#
public static void serialize(string nomFichier, Object objet)
        {
	        string fichier =                                                                                                                                 Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData), nomFichier);
            
            if (File.Exists(fichier))
            {
                File.Delete(fichier);
            }

            //Création du flux pour l'écriture dans le fichier
            FileStream fluxCreate = new FileStream(fichier, FileMode.Create, FileAccess.Write);

            //Création d'un objet pour le formatage en binaire des informations
            BinaryFormatter bf = new BinaryFormatter();

            try
            {
                //Sérialisation des objets de la collection
                bf.Serialize(fluxCreate, objet);
                //fermeture du flux
                fluxCreate.Close();
            }
            catch (Exception ex)
            {
                //Erreur de sérialisation
            }
        }
```

- Ensuite, nous devions créer une méthode pour la désérialisation :
```C#
public static Object deserialize(string nomFichier)
        {
            Object objet = null;

            string fichier = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData), nomFichier);

            if (File.Exists(fichier))
            {
                //ouverture d'un flux pour la lecture dans le fichier
                FileStream flux = new FileStream(fichier, FileMode.Open);

                //Création d'un objet pour le formatage en bianire des informations
                BinaryFormatter bfr = new BinaryFormatter();

                try
                {
                    //Récupération des données sérialisées
                    objet = bfr.Deserialize(flux);
                    flux.Close();
                }
                catch (Exception ex)
                {
                    //Erreur de désérialisation
                
                }
            }
            return objet;
        }
```

# Modifications des autres classes

Pour que la sérialisation fonctionne correctement il y a quelques modifications a effectuer sur les autres classes. 

- Juste avant la déclaration de la classe Profil : 
```C#
[Serializable]
```
>Indique quels objets sont sérialisables.

- Dans la classe contrôle, il faut créé une propriété statique pour stocker le nom du fichier à utiliser pour la sérialisation :
```C#
private static string monFichier = "saveprofil";
```
>Une propriété statique est une propriété que nous pouvions pas instancier.

- Ensuite, nous allons créer une méthode permettant de récupérer la sérialisation :
```C#
public static void RecupSerialize()
        {
            profil = (Profil)Serializer.deserialize(monFichier);
        }
```

- Puis, nous allons appeler cette méthode dans le *GetInstance()* permettant de récupérer la sérialisation au départ de l'application :
```C#
public static Controle Getinstance()
        {
            if(Controle.instance == null)
            {
                Controle.instance = new Controle();
                RecupSerialize();
            }
            return Controle.instance;
        }
```

- Puis, nous faisons des getters pour la sérialisation : 
```C#
public int GetSexe()
        {
            if (profil == null)
            {
                return 0;
            }
            else
            {
                return profil.GetSexe();
            }
        }
```

- Méthode *RecupProfil()* dans la classe MainActivity : 
```C#
private void RecupProfil()
        {
            if (controle.GetPoids() != 0)
            {
                txtPoids.Text = controle.GetPoids().ToString();
                txtTaille.Text = controle.GetTaille().ToString();
                txtAge.Text = controle.GetAge().ToString();

                rdFemme.Checked = true;
                if (controle.GetSexe() == 1)
                {
                    rdHomme.Checked = true;
                }

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
        }
```

>Cette méthode permet de valoriser les éléments affichés grâce aux données récupérées dans le contrôleur. Il faut donc appeler la méthode dans le *Init*. 