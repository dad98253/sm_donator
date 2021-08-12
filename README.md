# sm_donator
Basic Donator Interface 


Author:  toazron1

see https://forums.alliedmods.net/showthread.php?t=145542?t=145542

This plugin provides a backend for other plugins to interface with, acting as a core to simplify adding donator support to existing / new plugins.

Note: This is a repost of http://forums.alliedmods.net/showthread.php?t=122315 which broke from quick editing.

Information:

    After spending hours modifying/creating plugins with donator features only to have to go back into each one to make changes every time toazron1 needed to make a modification to the database layout he set out to develop an easier way.

    A thanks goes out to [AAA] Thrawn for pointing me in the right direction with the menu registering (and stealing his naming scheme).
    
Commands:

    !donators (or CHAT_TRIGGER) - Lets donators access the menu.
    sm_reloaddonators - Lets admins >= ADMFLAG_BAN reload the donator database.
    
Natives:

    PHP Code:
    /**
     * Register a menu item.
     * 
     * @param name            Name of the menu item.
     * @param func            Callback for menu items.
     * @return                Menu item ID.
     */
    native Donator_RegisterMenuItem(const String:name[], DonatorMenuCallback:callback);

    /**
     * Unregister a menu item.
     * 
     * @param name            Name of the menu item.
     * @param func            Callback for menu items.
     * @return                Bool
     */
    native Donator_UnregisterMenuItem(iItemId);

    /**  
     * Get a clients donator level, -1 if invalid
     * 
     * @param iClient    Client
     * @return            Donator level
     */
    native GetDonatorLevel(iClient);

    /**  
     * Sets a clients donator level
     *
     * @param iClient        Client
     * @param iLevel        Donator level
     * @return                Nothing
     */
    native SetDonatorLevel(iClient, iLevel);

    /**  
     * Returns True if a client is a donator, -1 if invalid
     * 
     * @param iClient    Client
     * @return            bool
     */
    native bool:IsPlayerDonator(iClient);

    /**  
     * Returns True if a steamid is a donator, -1 if invalid
     * 
     * @param iClient    Client
     * @return            bool
     */
    native bool:FindDonatorBySteamId(const String:szSteamId[]);

    /**  
     * Returns a clients connect message
     
     * @param iClient        Client
     * @return                Clients connect message
     */
    native GetDonatorMessage(iClient, const String:szMessage[], iLength);

    /**  
     * Sets a donators connect message
     *
     * @param iClient        Client
     * @param szMessage        Message to show on donator connect
     * @return                Nothing
     */
    native SetDonatorMessage(iClient, const String:szMessage[]); 

Forwards:

    PHP Code:
    /**
     * Forwards when a donator connects.
     * Note: This is before OnPostDonatorCheck - Cookies are not loaded here
     *
     * @param iClient        Client
     * @noreturn
     */
    forward OnDonatorConnect(iClient);

    /**
     * Forwards after OnPostAdminCheck for everyone.
     *
     * @param iClient        Client
     * @noreturn
     */
    forward OnPostDonatorCheck(iClient);

    /**
     * Forwards after the donators has been reladed with sm_reloaddonators.
     *
     * @param iClient        Client
     * @noreturn
     */
    forward OnDonatorsChanged(); 

Usage

    For example, if you wanted to make donators immune to the auto team balance in gScramble you can add an IsPlayerDonator(client) check into the IsValidTarget(...) check to skip a player who is a donator.

    Another feature is a centralized menu for plugins to register with which can minimize the amount of commands a player has to remember. This menu can be access by typing whatever CHAT_TRIGGER is set to (defaults !donators).

    To register a menu with the core:
    PHP Code:
    public OnAllPluginsLoaded()
    {
        if(!LibraryExists("donator.core")) SetFailState("Unabled to find plugin: Basic Donator Interface");
        Donator_RegisterMenuItem("MENU TITLE", MenuCallback);
    } 
    See one of the attached plugins for an example of a fully functional example.

Flat File Setup:

    The plugin looks for a text file named donators.txt (DONATOR_FILE) in the sourcemod/data directory
    The file format is as follows:
    PHP Code:
    STEAMID;LEVEL 

mySQL Setup:

    Note: If you are using the flat file option, you can skip this.

    If you do not already have an existing table you want to use (IE forums or another donator style setup.. if you do skip down a little) you need to create a new one (you can use a new database or a current one as long as it is a clean table).

    In this database make a new table, we will call it: donators
    If you decide to change the table name be sure and change the SQL_DBNAME define to your new table name.
    Code:

        CREATE TABLE IF NOT EXISTS `donators` (
          `steamid` varchar(64) default NULL,
          `tag` varchar(128) NOT NULL,
          `level` tinyint(1) NOT NULL default '1'
        )

    If you already have an existing table you want to use make sure that this table has the 3 columns needed (steamid, tag, level) Also, if you have them named different make sure you update the db_cols strings in the plugin.

    Also be sure and change SQL_CONFIG if you are not using default.

Example:

    Examples for visual learners:
    donator.chatcolor - gives donators the ability to set their chat color. If you want to use yellow (tf2) uncomment the define.

Note:

    This was developed for TF2 and I have not tested it in any other game, however, there is nothing TF2 specific within the core so there is no reason it shouldn't work.

Compiling Information:


    You must have loghelper.inc to compile donator.chatcolor (see https://github.com/dad98253/loghelper)

    The web compiler will not work.
    You must compile donator.core manually (it requires donator.inc)
