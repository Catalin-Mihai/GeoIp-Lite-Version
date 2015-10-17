#if defined _INC_GEOIP
	#endinput
#endif
#define _INC_GEOIP

#include <a_samp>
#include <a_http>

#if !defined _inc_sscanf2
	#tryinclude <sscanf2>

	#if !defined _inc_sscanf2
		#error The sscanf2 include must be included before <geoip>
	#endif
#endif

/*
#if !defined isnull
#define isnull(%1) \
	((!(%1[0])) || (((%1[0]) == '\1') && (!(%1[1]))))
#endif
*/

enum EX_GEO_DATA
{
	gAllData[256],
	gIP[32],
	gCountry[32], 
	gCity[32],
	gCountryCode[8], 
	gRegionCode[8],
	gRegion[32],
	gZipCode,
	gTimeZone[64],
	Float:gLatitude,
	Float:gLongitude,
	gMetroCode
};

// defines

#define Geo_GetIP(%0)					Ex_GeoData[%0][gIP]
#define Geo_GetAllData(%0)	 			Ex_GeoData[%0][gAllData]
#define Geo_GetCountry(%0)				Ex_GeoData[%0][gCountry]
#define Geo_GetCountryCode(%0)			Ex_GeoData[%0][gCountryCode]
#define Geo_GetCity(%0) 				Ex_GeoData[%0][gCity]
#define Geo_GetRegionCode(%0) 			Ex_GeoData[%0][gRegionCode]
#define Geo_GetRegion(%0) 				Ex_GeoData[%0][gRegion]
#define Geo_GetZipCode(%0) 				Ex_GeoData[%0][gZipCode]
#define Geo_GetTimeZone(%0) 			Ex_GeoData[%0][gTimeZone]
#define Geo_GetLatitude(%0) 			Ex_GeoData[%0][gLatitude]
#define Geo_GetLongitude(%0) 			Ex_GeoData[%0][gLongitude]
#define Geo_GetMetroCode(%0) 			Ex_GeoData[%0][gMetroCode]

//
#if !defined MAX_PLAYERS
	#define MAX_PLAYERS  100 // change it!
#endif
new Ex_GeoData[MAX_PLAYERS][EX_GEO_DATA];

public OnPlayerConnect(playerid)
{
	Ex_Geo_LoadPlayerData(playerid);
	#if defined Ex_Geo_OnPlayerConnect
	    return Ex_Geo_OnPlayerConnect(playerid);
	#else
	    return 1;
	#endif
}

static ConvertEncoding(string[])
{
	static const
		scRealChars[256] =
		{
			  0,   1,   2,   3,   4,   5,   6,   7,   8,   9,  10,  11,  12,  13,  14,  15,
			 16,  17,  18,  19,  20,  21,  22,  23,  24,  25,  26,  27,  28,  29,  30,  31,
			 32,  33,  34,  35,  36,  37,  38,  39,  40,  41,  42,  43,  44,  45,  46,  47,
			 48,  49,  50,  51,  52,  53,  54,  55,  56,  57,  58,  59,  60,  61,  62,  63,
			 64,  65,  66,  67,  68,  69,  70,  71,  72,  73,  74,  75,  76,  77,  78,  79,
			 80,  81,  82,  83,  84,  85,  86,  87,  88,  89,  90,  91,  92,  93,  94,  95,
			 96,  97,  98,  99, 100, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111,
			112, 113, 114, 115, 116, 117, 118, 119, 120, 121, 122, 123, 124, 125, 126, 127,
			128, 129, 130, 131, 132, 133, 134, 135, 136, 137, 138, 139, 140, 141, 142, 143,
			144, 145, 146, 147, 148, 149, 150, 151, 152, 153, 154, 155, 156, 157, 158, 159,
			160,  94, 162, 163, 164, 165, 166, 167, 168, 169, 170, 171, 172, 173, 174, 175,
			124, 177, 178, 179, 180, 181, 182, 183, 184, 185, 186, 187, 188, 189, 190, 175,
			128, 129, 130, 195, 131, 197, 132, 133, 134, 135, 136, 137, 138, 139, 140, 141,
			208, 173, 142, 143, 144, 213, 145, 215, 216, 146, 147, 148, 149, 221, 222, 150,
			151, 152, 153, 227, 154, 229, 155, 156, 157, 158, 159, 160, 161, 162, 163, 164,
			240, 174, 165, 166, 167, 245, 168, 247, 248, 169, 170, 171, 172, 253, 254, 255
		};
	if (ispacked(string))
	{
		// In packed strings nothing is over 255.
		for (new i = 0, len = strlen(string); i != len; ++i)
		{
			string{i} = scRealChars[string{i}];
		}
	}
	else
	{
		for (new i = 0, len = strlen(string), ch; i != len; ++i)
		{
			// Check if this character is in our reduced range.
			if (0 <= (ch = string[i]) < 256)
			{
				string[i] = scRealChars[ch];
			}
		}
	}
}

static Ex_Geo_LoadPlayerData(playerid)
{
	new IP[32], url[64];
	GetPlayerIp(playerid, IP, 32);
	printf("IP: %s", IP);
	format(url, 64, "freegeoip.net/csv/%s", IP);
	HTTP(playerid, HTTP_GET, url, "", "OnGeoHTTPResponse");
}

forward OnGeoHTTPResponse(playerid, response_code, data[]);
public OnGeoHTTPResponse(playerid, response_code, data[])
{
	if(response_code == 200)
	{
		ConvertEncoding(data);
		Ex_GeoData[playerid][gAllData][0] = EOS;
		strcat(Ex_GeoData[playerid][gAllData], data);
		sscanf(data, "p<,>s[32]s[8]s[32]s[8]s[32]s[32]is[64]ffi", Ex_GeoData[playerid][gIP], Ex_GeoData[playerid][gCountryCode], Ex_GeoData[playerid][gCountry], Ex_GeoData[playerid][gRegionCode], \
		Ex_GeoData[playerid][gRegion], Ex_GeoData[playerid][gCity], Ex_GeoData[playerid][gZipCode], Ex_GeoData[playerid][gTimeZone], Ex_GeoData[playerid][gLatitude], Ex_GeoData[playerid][gLongitude], Ex_GeoData[playerid][gMetroCode]);
	}
	else printf("[GeoIP] Couldn't get geoip stats for player %i. Response Code: %i", playerid, response_code);
}

// OnPlayerConnect Hook

#if defined _ALS_OnPlayerConnect
	#undef OnPlayerConnect
 #else
	#define _ALS_OnPlayerConnect
#endif

#define OnPlayerConnect Ex_Geo_OnPlayerConnect

#if defined Ex_Geo_OnPlayerConnect
	forward Ex_Geo_OnPlayerConnect(playerid);
#endif



