---
layout: default
title: Modificando Tema do Meu Nokia
date: 2010-02-14 02:03
author: brennords
comments: true
categories: [Hacking]
---
Enfim consegui um novo celular, esse pelo menos me dá mais opções pra eu ficar fuçando nele, já consegui fazer algumas coisas que não são ensinadas no manual (tão emocionante :P), mas não interessa, por que qualquer idiota, com o minimo de inteligência também consegue fazer o mesmo, mas acho que agora enfim fiz algo realmente útil :) .

Como meu celular é um Nokia, eu fui em busca de alguns temas pra ele, depois de baixar acho que mais ou menos uma dúzia deles, não me contentei com nenhum, mas sim com alguns detalhes de cada um deles, tipo um deles tinha um belo papel de parede, o outro bonitas cores, enquanto outro uns bons ícones, então em um momento de extrema inteligência, pensei: "por que não criar um, do jeito que eu quero?".

Nem tava muito afim de criar um tema do zero (sim é preguiça mesmo), até por que nunca tentei, e não fazia muito idéia de como criar um (sim eu sou burro como todo mundo também :P).

Os arquivos de tema tem extensão nth (sigla de Nokia Theme, eu acho), mas abrem na moral com o WinRAR, depois de observar isso, começei a dar uma olhada de como eram os temas "por dentro".

Percebi que existia um arquivo principal pra qualquer tema, era o theme_descriptor.xml, era onde iam todas as configurações, como cores das fontes, nomes de wallpapers, screensavers, ícones e tals.

Além do Theme_Descriptot.xml, existiam também dentro de cada tema, seus ícones (a maioria em formato PNG), Wallpapers (maioria era JPG), RingTones quando o tema os oferecia (no formato .mp3), animações dos menus e tals (em GIF), e alguns arquivos m3g e acc.

Ok, peguei um arquivo .nth, e começei a edita-lo por curiosidade, pra ver se eu substituisse os arquivos, não corromperia o tema nem nada, consegui modificar um tema (ficou meio precário).

Ainda assim não me agradei com ele, ele usava uma imagem flash (swf) como Wallpaper (e o tema ficou perfeito depois de umas mudanças, não queria modificar outro tema, só por causa do problema do Wallpaper), então eu teria que usar outro arquivo swf, para poder mudar o papel de parede, mas ai então eu tive a idéia (brilhante por sinal lol), de me aventurar no arquivo XML.

Era mais simples do que eu imaginava, então começei a editar o arquivo de 34 linhas, troquei as cores das fontes (tem alguns temas que deixam as fontes, praticamente INVISIVEIS ¬¬'), e consegui substituir o arquivo swf por um arquivo jpg (o que foi muito dificil lol).

Esse foi o resultado do XML editado:

```

?xml version=1.0 encoding=UTF-8?
!DOCTYPE theme PUBLIC -//NOKIA//DTD THEME 2.0//EN theme.dtd
theme name=Brenn0NokiaTheme version=2.0
 colors calendar_highlight_color=0xff00ff display=main forms_selected_font_color=0xffffff forms_unselected_font_color=0xff00ff grid_highlight_color=0xffffff grid_menu_font_color=0xffffff grid_menu_highlight_font_color=0xffffff header_font_color=0x000000 idle_font_color=0xffffff idle_font_outline_color=0x333333 idle_softkey_area_font_color=0xc0c0c0 idle_status_area_font_color=0x000000 menu_font_color=0xffffff menu_highlight_font_color=0x000000 shortcut_bar_popup_background_color=0x333300 softkey_font_color=0x7cfc00 status_area_font_color=0x1e90ff/
 menu_item animating_grid=grid_view_icon$menu_item$ams_messages_1.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$ams_messages_1.png item_id=ams_messages list_view_icon=grid_view_icon$menu_item$ams_messages_1.png/
 menu_item animating_grid=grid_view_icon$menu_item$applications_1.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$applications_1.png item_id=applications list_view_icon=grid_view_icon$menu_item$applications_1.png/
 menu_item animating_grid=qgn_menu_grid_brew.m3g grid_view_icon=qgn_menu_grid_brew.png item_id=brew list_view_icon=qgn_list_large_brew.png/
 menu_item animating_grid=grid_view_icon$menu_item$callregister_1.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$callregister_1.png item_id=callregister list_view_icon=grid_view_icon$menu_item$callregister_1.png/
 menu_item animating_grid=grid_view_icon$menu_item$camera_1.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$camera_1.png item_id=camera list_view_icon=grid_view_icon$menu_item$camera_1.png/
 menu_item animating_grid=qgn_menu_grid_service.m3g grid_view_icon=qgn_menu_grid_service.png item_id=cdmacust list_view_icon=qgn_list_large_services.png/
 menu_item animating_grid=grid_view_icon$menu_item$contacts_4.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$contacts_4.png item_id=contacts list_view_icon=grid_view_icon$menu_item$contacts_4.png/
 menu_item animating_grid=qgn_menu_grid_extras.m3g grid_view_icon=qgn_menu_grid_extras.png item_id=extras list_view_icon=qgn_list_large_extras.png/
 menu_item animating_grid=grid_view_icon$menu_item$gallery_1.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$gallery_1.png item_id=gallery list_view_icon=grid_view_icon$menu_item$gallery_1.png/
 menu_item animating_grid=qgn_menu_grid_service.m3g grid_view_icon=qgn_menu_grid_service.png item_id=goto list_view_icon=qgn_list_large_services.png/
 menu_item animating_grid=grid_view_icon$menu_item$media_1.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$media_1.png item_id=media list_view_icon=grid_view_icon$menu_item$media_1.png/
 menu_item animating_grid=animating_grid$menu_item$messages_2.png app_specific_bg=music 002_1_1.jpg grid_view_icon=animating_grid$menu_item$messages_2.png item_id=messages list_view_icon=animating_grid$menu_item$messages_2.png/
 menu_item animating_grid=grid_view_icon$menu_item$organizer_2.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$organizer_2.png item_id=organizer list_view_icon=grid_view_icon$menu_item$organizer_2.png/
 menu_item animating_grid=grid_view_icon$menu_item$push_to_talk_5.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$push_to_talk_5.png item_id=push_to_talk list_view_icon=grid_view_icon$menu_item$push_to_talk_5.png/
 menu_item animating_grid=grid_view_icon$menu_item$services_1.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$services_1.png item_id=services list_view_icon=grid_view_icon$menu_item$services_1.png/
 menu_item animating_grid=grid_view_icon$menu_item$settings_2.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$settings_2.png item_id=settings list_view_icon=grid_view_icon$menu_item$settings_2.png/
 menu_item animating_grid=grid_view_icon$menu_item$simatk_1.png app_specific_bg=music 002_1_1.jpg grid_view_icon=grid_view_icon$menu_item$simatk_1.png item_id=simatk list_view_icon=grid_view_icon$menu_item$simatk_1.png/
 menu_item animating_grid=qgn_menu_grid_extras.m3g grid_view_icon=qgn_menu_grid_extras.png item_id=wireless_village list_view_icon=qgn_list_large_extras.png/
 wallpaper main_display_graphics=Music clock 6.jpg/
 screensaver main_display_graphics=Music clock 2.jpg/
 background grid_menu_bg=grid_menu_bg$background_1.jpg idle_softkey_area_bg=blank.png idle_status_area_bg=blank.png main_default_bg=music 002_1_1.jpg note_bg=236x114v2_note_back.png/
 calendar_bg april=january$calendar_bg.jpg august=january$calendar_bg.jpg december=january$calendar_bg.jpg february=january$calendar_bg.jpg january=january$calendar_bg.jpg july=january$calendar_bg.jpg june=january$calendar_bg.jpg march=january$calendar_bg.jpg may=january$calendar_bg.jpg november=january$calendar_bg.jpg october=january$calendar_bg.jpg september=january$calendar_bg.jpg/
 radio_audio_bg audio_bg=grid_menu_bg$background_1.jpg radio_bg=grid_menu_bg$background_1.jpg/
 softkey_bg left=right$softkey_bg.png middle=rightsoftkey_bg_1_1.png right=right$softkey_bg.png/
 wait_graphics src=wait2_graphics.gif/
 highlight active_idle_row=list$highlight.png active_idle_shortcut_bar=grid$highlight_4.png forms_selected=224x44_form_unselected.png forms_unselected=224x44_form_unselected.png grid=black_1.png list=list$highlight_1.png reorder=reorderhighlight1.png tab=grid.png/
 tones msg_alert=msg_alert$tones.mp3 ringtone=ringtone$tones.mp3/
 transformation_open duration=1000 tone=tone$transformation_close.aac/
 transformation_close duration=1000 tone=tone$transformation_close.aac/
/theme

```

Nada sofisticado, até por que não sou um bom programador, me classifico como apenas um curioso, que fica fazendo várias merdas até acertar xD (sim editei muito o tema pra dar certo).

Não sei se ficou tão bonito (pra mim ficou sim :P), mas se quiser ver os outros detalhes, pode encontrar o tema nesse link: <a href="http://www.megaupload.com/?d=FBB4WLB9" target="_blank">http://www.megaupload.com/?d=FBB4WLB9</a> .

E esse é o fim de mais um post inútil :P .

Bjx <strong>:<span style="color:#ff0000;">*</span></strong>
