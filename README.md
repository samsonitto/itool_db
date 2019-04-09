# TTZC0800 (Tietokannat) kurssin Harjoitustyö

# Sisällysluettelo

* [Vaatimusmäärittely](#arkkitehtuurikuvaus)
    * [Johdanto](#sekvenssikaavio-kirjautuminen)
    * [Yleiskuvaus](#tilakonekaavio)
    * [Toiminnot](#käsitemalli)
    * [Ulkoiset liittymät](#luokkakaavio)
    * [Muut ominaisuudet](#sijoittelunäkymä)

#Vaatimusmäärittely

### Työkalujenlainauspalvelu (Über for tools)

* "iTool"-tietokanta
* Työryhmä: Samson Azizyan (M3156), Joel Aalto (), Jaber Askari ()
* Versio 0.1 9.4.2019

## Johdanto

Tietokanta tehdään työkalulainaussovellusta varten. Yrittäjyys tunnilla piti väkisin keksiä ideoita palvelua (sovellusta) varten. Voitetiin meidän iTool idealla huulirasvat.
Tarkoitus on tehdä tietokantaratkaisun hiekkalaatikossa toimivaa sovellusta varten. Samson toteuttaa käyttöliittymän sovellusta varten käyttöliittymäohjelmointi kurssin projektityönä.
Tavoitteena on pystyä vuokraamaan muden sovelluksen käyttäjien vuokralle jätettyjä työkaluja.

## Yleiskuvaus

```csharp
foreach (Comment reply in comments)
                {
                    if (reply.CommentParentID == item.CommentID)
                    {
                        User us = DB.GetToolOwnerFromMysql(reply.userID);

                        Label lbComment = new Label();
                        lbComment.Content = $"Comment #{reply.CommentID}";
                        lbComment.FontSize = 12;
                        lbComment.FontWeight = FontWeights.Bold;
                        Label lb = new Label();
                        lb.Content = $"{us.FirstName} {us.LastName}, User ID: #{us.UserID}   {item.DateTime.ToString()}   In reply to comment #{item.CommentID}";
                        lb.FontSize = 12;
                        lb.FontWeight = FontWeights.Bold;

                        TextBlock reply1 = new TextBlock();
                        reply1.Text = reply.Text;
                        reply1.VerticalAlignment = VerticalAlignment.Top;
                        reply1.HorizontalAlignment = HorizontalAlignment.Left;
                        reply1.FontSize = 12;
                        reply1.TextWrapping = TextWrapping.Wrap;

                        StackPanel spReply = new StackPanel();
                        spReply.Margin = new Thickness(margin += 20, 10, 0, 0);
                        spReply.Orientation = Orientation.Vertical;
                        spReply.HorizontalAlignment = HorizontalAlignment.Left;
                        spReply.Children.Add(lbComment);
                        spReply.Children.Add(lb);
                        spReply.Children.Add(reply1);

                        lbxComments.Items.Add(spReply);
                    }
                }
```