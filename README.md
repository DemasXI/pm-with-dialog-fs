# pm-with-dialog-fs

Pm dengan Dialog 
https://imgur.io/evV7FXE

```															/*********************************************************************************
															*                     Private Message Message (with Dialogs)                     *
															*                                 VERSION 1.0                                    *
															*                                  by xGame                                      *
															*********************************************************************************/

#include <a_samp>
#include <Dini>

new str[250];

#define Send 1197
#define Recive 1196

#define Red "{FF0000}"
#define White "{FFFFFF}"
#define Green "{00FF00}"

#define dcmd(%1,%2,%3) if ((strcmp((%3)[1], #%1, true, (%2)) == 0) && ((((%3)[(%2) + 1] == 0) && (dcmd_%1(playerid, "")))||(((%3)[(%2) + 1] == 32) && (dcmd_%1(playerid, (%3)[(%2) + 2]))))) return 1

enum acc{
	ID,
	Message[250],
	Enabled
};
new Info[MAX_PLAYERS][acc];

public OnFilterScriptInit()
{
	print("\n--------------------------------------");
	print(" Filterscript PRIVATE MESSAGE");
	print("                     by Demasxi");
	print(" Succesfull loaded!");
	print("--------------------------------------\n");
	return 1;
}

public OnFilterScriptExit()
{
	print("\n--------------------------------------");
	print(" Filterscript PRIVATE MESSAGE");
	print("                     by DemasXi");
	print(" Succesfull unloaded!");
	print("--------------------------------------\n");
	return 1;
}

public OnPlayerConnect(playerid)
{
	Info[playerid][ID] = -1;
	format(Info[playerid][Message],250,"");
	if(!dini_Exists(Account(playerid)))return dini_Create(Account(playerid)),dini_IntSet(Account(playerid),"Enabled",1);
	Info[playerid][Enabled] = dini_Int(Account(playerid),"Enabled");
	return 1;
}

public OnPlayerCommandText(playerid, cmdtext[])
{
	dcmd(mpm,3,cmdtext);
	return 0;
}

dcmd_mpm(playerid,params[])
{
	#pragma unused params
	if(Info[playerid][Enabled] == 1)
	{
	    Info[playerid][Enabled] = 0;
	    SendClientMessage(playerid,-1,"[INFO]: "#Red"Now, you can't recive private message!");
	}
	else
	{
		Info[playerid][Enabled] = 1;
		SendClientMessage(playerid,-1,"[INFO]: "#Green"Now, you can recive private message!");
	}
	dini_IntSet(Account(playerid),"Enabled",Info[playerid][Enabled]);
	return 1;
}

public OnDialogResponse(playerid, dialogid, response, listitem, inputtext[])
{
	if(dialogid == Send)
	{
		new str1[250];
	    if(!response || strlen(inputtext) == 0)return SendClientMessage(playerid,-1,"[INFO]: "#Red"Dialog closed!");
		format(str1,sizeof(str1),"%s",Info[playerid][Message]);
		if(strlen(str1) == 0)
		{
		    new str3[250];
		    format(str3,sizeof(str3),""#White"%s: "#Green"%s",Name(playerid),inputtext);
			format(Info[playerid][Message],sizeof(str),"%s",str3);
		}
		else
		{
		    new str2[250],str3[250];
		    format(str2,sizeof(str2),"%s",Info[playerid][Message]);
		    format(str3,sizeof(str3),""#White"%s: "#Green"%s",Name(playerid),inputtext);
		    format(Info[playerid][Message],sizeof(str),"%s\n%s",str2,str3);
		}
		ShowPlayerDialog(Info[playerid][ID],Recive,DIALOG_STYLE_MSGBOX,"PrivateMessage",Info[playerid][Message],"Ok","");
		Info[playerid][ID] = -1;
	}
	if(dialogid == Recive)
	{
	    if(response)
		{
		    format(Info[playerid][Message],sizeof(str),"");
		}
	}
	return 1;
}

public OnPlayerClickPlayer(playerid, clickedplayerid, source)
{
	if(playerid != clickedplayerid)
	{
		if(Info[clickedplayerid][Enabled] == 0)return SendClientMessage(playerid,-1,"[INFO]: "#Red"User have been disabled PrivateMessage!");
		Info[playerid][ID] = clickedplayerid;
		ShowPlayerDialog(playerid,Send,DIALOG_STYLE_INPUT,"Message","Please, insert the message!","Ok","Exit");
	}
	return 1;
}

stock Account(playerid)
{
	format(str,sizeof(str),"Message/%s.txt",Name(playerid));
	return str;
}

stock Name(playerid)
{
	new name[250];
	GetPlayerName(playerid,name,sizeof(name));
	return name;
}
