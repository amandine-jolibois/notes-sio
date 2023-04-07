---
{"dg-home":false,"dg-publish":true,"permalink":"/epreuve-pratique/cas-site-france-mobilier/","dgPassFrontmatter":true}
---

- [[Accueil\|Accueil]]

# Mission A :
#MissionA

![Pasted image 20230407131116.png](/img/user/Pasted%20image%2020230407131116.png)

## Création du métier Gamme 

Permettant d'initialiser les variables de la base de données de la table gamme. 

```php
<?php
    class Gamme
    {
        private $gam_id;
        private $gam_libelle;
        
        public function __construct($id,$libelle)
        {
            $this->gam_id=$id;
            $this->gam_libelle=$libelle;
        }

        public function GetId()
        {
            return $this->gam_id;
        }

        public function GetLibelle()
        {
            return $this->gam_libelle;
        }
    }
?>
```


## Création du modèle gamme 

Permettant de crée les deux fonctions *GetListe()* et *GetGam()* :
>GetListe() -> Permet de récupérer la liste de toutes gammes. 
>GetGam() -> Permet récupérer une gamme sélectionnée.  

```php
<?php
    require_once "m_generique.php";
    require_once "metiers/Gamme.php";
    class m_gamme extends m_generique
    {
        public function GetListe()
        {
            $resultat=array();
            $this->connexion();
            $req="select * from gamme order by gam_libelle";
            $res=mysqli_query($this->GetCnx(),$req);
            $ligne=mysqli_fetch_assoc($res);
            while ($ligne)
            {
                $gam=new Gamme($ligne["gam_id"],$ligne["gam_libelle"]);
                $resultat[]=$gam;
                $ligne=mysqli_fetch_assoc($res);
            }
            $this->deconnexion();
            return $resultat;
        }

        public function Getgam($id)
        {
            $resultat=null;
            $this->connexion();
            $req="select * from gamme where gam_id=".$id;
            $res=mysqli_query($this->GetCnx(),$req);
            $ligne=mysqli_fetch_assoc($res);
            if($ligne)
            {
                $gam=new Gamme($ligne["gam_id"],$ligne["gam_libelle"]);
                $resultat=$gam;
            }
            $this->deconnexion();
            return $resultat;
        }
    }
?>
```


## Modification de la vue menu 

>Ajout de la liste déroulante dans le menu :

```php
<select name="gam" size="1">
            <option selected value="0">Toutes les gammes</option>
            <?php
                foreach ($this->data['lesGammes'] as $uneGamme)
                {	         echo'<optionvalue="'.$uneGamme>GetId().'">'.$uneGamme>GetLibelle().'</option>';
                }
</select>
```


## Modification du contrôleur menu 

>Ajoute les données dans la liste déroulante : 

```php
$data['lesGammes']=$this->modele_gamme->GetListe();
```


## Modification du contrôleur consulter produit 

>Ajout de la variable, du constructeur et l'appelle de la fonction GetListe() :

```php
require_once "modeles/m_gamme.php";

private $modele_gamme;

$this->modele_gamme=new m_gamme();

$this->data['laGamme']=$this->modele_gamme->GetGam($idGamme);
```

>Ajout du paramètre gamme dans l'appelle de la fonction pour les produits : 

```php
$this->data['lesProduits']=$this->modele_produit->GetListe($idCategorie,$idGamme);
```


## Modification du modèle produit / vue listePdt

>Ajout de toute les conditions pour les différentes requêtes SQL : 

```php
public function GetListe($categ,$gam)
        {
            $resultat=array();
            $this->connexion();
            if ($categ==0 && $gam==0)
            {
                $req="select * from produit";
            }elseif($categ==0 && $gam!=0)
            {
                $req="select * from produit where pro_gamme=".$gam;
            }
            else
            {
                $req="select * from produit where pro_categorie=".$categ;
            }
         }
```

>Ajout des conditions pour les différents affichages des produits : 

```php
if (is_null($this->data['laCategorie']) && is_null($this->data['laGamme']))
            {
                echo '<h2>Tous les produits</h2>';
            }elseif(is_null($this>data['laCategorie'])&&!is_null($this>data['laGamme']))
            {
                echo '<h2>Gamme '.$this->data['laGamme']->GetLibelle().'</h2>';
            }
            else
            {
                echo '<h2>Catégorie '.$this>data['laCategorie']>GetLibelle().'</h2>';
            }
```


## Modification de l'index 

>Ajout du paramètre $\_Get['gam'] dans l'appelle de la fonction :

```php
$controleur->action_listeProduits($_GET['categ'], $_GET['gam']);
```



# Mission B 
#MissionB

![Pasted image 20230407133252.png](/img/user/Pasted%20image%2020230407133252.png)

## Modifications de la base de données 

>Ajout de la colonne gamPref dans la table abonne en clé étrangère : 

```SQL
ALTER TABLE abonne ADD gamPref INT;
ALTER TABLE abonne ADD CONSTRAINT FOREIGN KEY (gamPref) REFERENCES gamme(gam_id);
```

## Modification de la vue saisie Abonne

>Ajout de la liste déroulante : 

```php
<select name="gamPref" size="1">
            <option selected value="0">La gamme favorite</option>
            <?php
                foreach ($this->data['lesGammes'] as $uneGamme)
                {                  echo'<optionvalue="'.$uneGamme>GetId().'">'.$uneGamme>GetLibelle().'</option>';
                }          
</select>
```



## Modification du métier Abonne 

>Ajout de la variable gamPref, constructeur et getter : 

```php
private $abo_gamPref;

$this->abo_gamPref=$gamPref;

public function GetGamPref()
        {
            return $this->abo_gamPref;
        }
```



## Modification du modèle Adonne / contrôleur AjouterAbonne

>Ajout des paramètres gamPref :

```php
$abonne=new Abonne($id,$email,$date,$ok,$gamPref);
$req="insertintoabonnevalues('".$id."','".$email."','".$date."',".$ok.",".$gamPref.";
```

```php
$abonne=$this->modele_abonne->Ajouter($id,$email,$date,$ok,$gamPref);
```



## Modification de l'index

>Ajout du paramètre gamme dans l'appelle de la fonction : 

```php
$controleur->action_ajout($_GET['email'],$_GET['gamPref']);
```

