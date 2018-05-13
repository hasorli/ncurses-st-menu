Interface - API
===============

# Structures

## Structure `ST_MENU`

An array of this structures is used as imput template.

```c
typedef struct _ST_MENU
{
	char	*text;						/* text of menu item, possible specify accelerator by ~ */
	int		code;						/* code of menu item (optional) */
	char	*shortcut;					/* shortcut text, only printed (optional) */
	int		options;					/* locked, marked, ... (optional) */
	struct _ST_MENU *submenu;			/* reference to nested menu (optional) */
} ST_MENU;
```

### Example

```c
ST_MENU menubar[] = {
	{"~L~eft", 60, NULL, 0, _left},
	{"~F~ile", 61, NULL, 0, _file},
	{"~C~ommand", 62, NULL, 0, _command},
	{"~O~ptions", 63, NULL, ST_MENU_OPTION_DISABLED, NULL},
	{NULL, -1, NULL}
};
```

## Structure `ST_MENU_STATE`

This is opaque structure - the content is protected, that holds state data
and some cached data used for faster visualization.

```c
struct ST_MENU_STATE;
```

## Structure `ST_MENU_CONFIG`

This structure holds configuration defined visual form and behave pull down and menubar menus.

```c
typedef struct
{
	bool	force8bit;
	char   *encoding;
	const char   *language;
	bool	wide_vborders;			/* wide vertical menu borders like Turbo Vision */
	bool	wide_hborders;			/* wide horizontal menu borders like custom menu mc */
	bool	draw_box;				/* when true, then box is created */
	bool	left_alligned_shortcuts;	/* when true, a shortcuts are left alligned */
	bool	extra_inner_space;		/* when true, then there 2 spaces between text and border */
	int		shadow_width;			/* when shadow_width is higher than zero, shadow is visible */
	int		menu_background_cpn;	/* draw area color pair number */
	int		menu_background_attr;	/* draw area attributte */
	int		menu_shadow_cpn;		/* draw area color pair number */
	int		menu_shadow_attr;		/* draw area attributte */
	int		accelerator_cpn;		/* color pair of accelerators */
	int		accelerator_attr;		/* accelerator attributes */
	int		cursor_cpn;				/* cursor color pair */
	int		cursor_attr;			/* cursor attributte */
	int		cursor_accel_cpn;		/* color pair of accelerator on cursor row */
	int		cursor_accel_attr;		/* attributte of of accelerator on cursor row */
	int		disabled_cpn;			/* color of disabled menu fields */
	int		disabled_attr;			/* attributes of disabled menu fields */
	int		shortcut_space;			/* spaces between text and shortcut */
	int		text_space;				/* spaces between text fields (menubar), when it is -1, then dynamic spaces (FAND) */
	int		init_text_space;		/* initial space for menu bar */
	int		menu_bar_menu_offset;	/* offset between menu bar and menu */
	int		inner_space;			/* space between draw area and border, FAND uses 2 spaces */
	int		extern_accel_text_space;	/* space between external accelerator and menu item text */
	int		submenu_tag;			/* symbol used for submenu tag */
	int		submenu_offset_y;		/* offset for submenu related to right border of parent menu window */
	int		submenu_offset_x;		/* offset for submenu related to cursor in parent menu window */
} ST_MENU_CONFIG;
```

This structure can be simply initialized by function `st_menu_load_style()`.

```c
ST_MENU_CONFIG	config;

setlocale(LC_ALL, "");

/* Don't use UTF when terminal doesn't use UTF */
config.encoding = nl_langinfo(CODESET);
config.language = uc_locale_language();
config.force8bit = strcmp(config.encoding, "UTF-8") != 0;

/* load style, possible alternatives: ST_MENU_STYLE_MC, ST_MENU_STYLE_DOS */
st_menu_load_style(&config, 11, 2);
```

Available styles:
```c
#define ST_MENU_STYLE_MCB			0
#define ST_MENU_STYLE_MC			1
#define ST_MENU_STYLE_VISION		2
#define ST_MENU_STYLE_DOS			3
#define ST_MENU_STYLE_FAND_1		4
#define ST_MENU_STYLE_FAND_2		5
#define ST_MENU_STYLE_FOXPRO		6
#define ST_MENU_STYLE_PERFECT		7
#define ST_MENU_STYLE_NOCOLOR		8
#define ST_MENU_STYLE_ONECOLOR		9
#define ST_MENU_STYLE_TURBO			10
#define ST_MENU_STYLE_PDMENU		11
#define ST_MENU_STYLE_OLD_TURBO		12
```

# Functions
```c
extern int st_menu_load_style(ST_MENU_CONFIG *config, int style, int start_from_cpn);
extern void st_menu_set_desktop_panel(PANEL *pan);

extern struct ST_MENU_STATE *st_menu_new(ST_MENU_CONFIG *config, ST_MENU *menu, int begin_y, int begin_x, char *title);
extern struct ST_MENU_STATE *st_menu_new_menubar(ST_MENU_CONFIG *config, ST_MENU *menu);

extern void st_menu_post(struct ST_MENU_STATE *mstate);
extern void st_menu_unpost(struct ST_MENU_STATE *mstate, bool close_active_submenu);
extern bool st_menu_driver(struct ST_MENU_STATE *mstate, int c, bool alt, MEVENT *mevent);
extern void st_menu_delete(struct ST_MENU_STATE *mstate);
extern void st_menu_save(struct ST_MENU_STATE *mstate, int *cursor_rows, int max_rows);
extern void st_menu_load(struct ST_MENU_STATE *mstate, int *cursor_rows);

extern ST_MENU *st_menu_selected_item(bool *activated);

extern bool st_menu_set_option(struct ST_MENU_STATE *mstate, int code, int option);
extern bool st_menu_reset_option(struct ST_MENU_STATE *mstate, int code, int option);
```

* `st_menu_load_style` - fill ST_MENU_CONFIG from predefined styles. `start_from_cpn` is first free
  color pair. This function returns next free color pair.

* `st_menu_set_desktop_panel` - allow to specify some panel used like desktop. The content of 
   related window is used as source for shadow drawing.

* `st_menu_new` - create state variable for pull down menu from template. `st_menu_new_menubar` - same like
  previous function - creates menubar menu.

  

