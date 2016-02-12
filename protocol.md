# PROTOCOL DOCUMENTATION 

## Stuff (Data to share)
        
 - Music files (.mp3)
 - as an extension, this could also be a set of files (albums)
 - each *stuff* will be split into books
 
 
## Library (description file)
        
 - Text file with .libr extension
 - Store it on a server to make it accessible to everybody
 - It contains the following informations
    - the address of the *hub* (to be defined)
    - the name of the *stuff*
    - the size of the *stuff*
    - the size used for *books* (fixed at 8KB)
    - Array containing SHA1 of each *book* :
       - First element : number of *books*
       - Each following element : SHA1 of a *book*
       - Elements are separated by a " ; " 
    - Informations are separated by a " ! "

## Book (piece of *stuff*)
   
- This is the unit of exchange: *players* exchange *books*
- *Books* are all of the same size, except the last one.
- The size of a book is 8KB.
- SHA1  Its a type of checksum that we will use to check the integrity of a book
- It is stored in the library file
- It is checked by the player when receiving a book (or when restarting with from a partially downloaded stuff)
   
   
##   Player (“peer” in BitTorrent)
#### This is a program exchanging books with others *players*
1. Loads a _library_ file
2. It finds others players by querying the hub
3. It remembers (in memory) the SHA1 of each book
4. It remembers what book it has, and shares this information with others players
5. It can ask books to other players
6. It can provide books to other players
7. Querying the hub each minute to "tell it" what is available to be shared and to check if there is new players to use for downloading
  
##  Hub (“tracker” in BitTorrent)
- this is a program that maintains an updated list of all players exchanging the stuff
- it provides, on demand by a player, a list of other players to connect to
- it does nothing else: it does not download or upload any book, does not know what player has what books, etc.

To have a functional peer-to-peer system, you will thus need to write 3 programs:

  - A plain program named librarifier (used before starting to share some stuff) that generates the .libr file corresponding to some stuff.
  
  - A networked program named hub (that will be started only once for any library) which role is described above.
  
  - A networked program named player (that will be started by each user that want to download or share the stuff) that .
  
  
  
## List of functions

##### From Player to Hub
 - void iAmOnline(HubAdress)
 	send a package to hub, in order to add the current player to the available player online
 	
 - ListPlayerAndAdress playersList(HubAdress,NameStuff) : 
 ask for a list of players sharing a stuff, and serveradress of libr.
 
 - boolean submitNewStuff(HubAdress,NameStuff, ServerAdress) : 
 submit new file to the hub : yes if it s ok, false if not (name already exist, name is null etc..) 
 - void iAmOffline(HubAdress)
 
##### From Player to Player
- Book askBook(PlayerAdress, idBook)
- boolean sendBook(PlayerAdress, Book) 
- boolean receiveBook(PlayerAdress)

##### From Hub to Player
 - ping(PlayerAdress) : is the player still online ?

##### Message : 
Bytes[] :
 Name of function ; argument1 , argument2 .., argument n ; client address ;
 
 Name of function : code names for functions. List of codename :
 - iAmOnline : online
 - iAmOffline : offline
 - playersList : list
 - submitNewStuff : newstuff
 - askBook : askbook
 - sendBook : sendbook
 - ping : ping
 
 Examples :
 
 A player 192.168.1.0 asks for list to hub 192.168.1.12 : 
 player send message [list;X;192.168.1.0] ,
 hub receives it, understands list action and return the list concerning stuff X :
 [list;address2,address3,address4;192.168.1.12]
 Then the client, who was waiting for answer, receive list message and store addresses.
 
 A player A asks for a book with askbook message. When receive askbook request, a player B check if he got the book, and then answer with sendbook request.
 Meanwhile the player A was waiting for B answer, if answer arrives and is sendbook request message, then check the book with SHA1, if everything is right, then add book to the already downloaded books.
 



