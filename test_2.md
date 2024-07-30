


```rust


use std::collections::HashMap;

use serde_json::Value;
use tao::{event_loop::EventLoopWindowTarget, window::{
    CursorIcon, 
    ResizeDirection, 
    UserAttentionType
}};
use wry::{
    MemoryUsageLevel, ProxyEndpoint, RequestAsyncResponder, WebContext, WebView, WebViewAttributes, WebViewBuilder, WebViewExtWindows, RGBA
};

use image::ImageFormat;
use tao::{
/*     dpi::{
        LogicalPosition, 
        LogicalSize, 
        LogicalUnit, 
        PhysicalPosition, 
        PhysicalSize, 
        PhysicalUnit, 
        PixelUnit, 
        Position, 
        Size
    },  */
    event::{Event, WindowEvent}, 
    event_loop::{ControlFlow, EventLoop, EventLoopBuilder, EventLoopProxy}, 
    window::{
        Fullscreen, Icon, Theme, Window, WindowAttributes, WindowBuilder, WindowSizeConstraints,
    },
    
};

use tao::window::WindowId;



#[allow(dead_code)]

pub(crate) enum UserEvent {
    CloseWindow(WindowId),
    NewTitle(WindowId, String),
    NewWindow,
  }

  pub struct Nexium {
    event_loop: EventLoop<UserEvent>,
    proxy:Option<EventLoopProxy<UserEvent>>,
    webview_core: WebViewAttributes,
    window_core: WindowAttributes,
    webview:Option<WebView>,
    window:Option<Window>,
    web_context:Option<WebContext>,
    config:HashMap<String,Value>
    
}

impl Nexium {
    pub fn new() -> Self {
        Self {
            event_loop: EventLoopBuilder::with_user_event().build(),
            proxy:None,
            webview_core: Default::default(),
            window_core: Default::default(),
            webview:None,
            window:None,
            web_context:Default::default(),
            config:HashMap::new()
        }
    }


    #[allow(dead_code)]
    pub fn webview_builder_use_url(&mut self,  url: String){
        self.webview_core.url = Some(url.into());
        self.webview_core.headers = None;
        self.webview_core.devtools = true;
      
    
    }


    #[allow(dead_code)]
    pub fn webview_is_devtools_open(&mut self){
        if let Some(view) = self.webview.take(){
            let devtools_state= view.is_devtools_open();
            println!("devtools_state: {}", devtools_state)
        }
      
    
    }
    #[allow(dead_code)]
    pub fn webcontext_data_directory(&mut self){
        if let Some(context) = self.web_context.take(){
            let data_directory= context.data_directory();
            println!("data_directory: {:?}", data_directory)
        }
      
    
    }
    #[allow(dead_code)]
    pub fn webview_evaluate_script(&mut self,  js: &str){
        if let Some(view) = self.webview.take(){
            let script_state= view.evaluate_script(js);
            println!("webview_evaluate_script_state: {:?}", script_state)
        }
      
    
    }





    #[allow(dead_code)]
    pub(crate) fn send_proxy_event(&mut self, event:UserEvent){
        if let Some(proxy) = self.proxy.take(){
            let _ = proxy.send_event(event);
            
        }
      
    
    }


   

    pub fn run(
        mut self, 
        
    ) {

        let  proxy = self.event_loop.create_proxy();

        self.proxy = Some(proxy);

        let mut window_builder = WindowBuilder::new();

        window_builder.window = self.window_core;

        let main_window = window_builder.build(&self.event_loop).unwrap();
        /*         

        main_window.available_monitors();
        main_window.current_monitor()
        main_window.cursor_position()
        main_window.drag_resize_window(direction)
        main_window.drag_window()
        main_window.fullscreen()
        main_window.id()
        main_window.inner_position()
        main_window.inner_size()
        main_window.is_closable()
        main_window.is_decorated()
        main_window.is_focused()
        main_window.is_maximizable()
        main_window.is_maximized()
        main_window.is_minimizable()
        main_window.is_minimized()
        main_window.is_resizable()
        main_window.is_visible()
        main_window.monitor_from_point(x, y)
        main_window.outer_position()
        main_window.outer_size()
        main_window.primary_monitor()
        main_window.request_redraw()
        main_window.request_user_attention(request_type)
        main_window.scale_factor()
        main_window.set_always_on_bottom(always_on_bottom)
        main_window.set_always_on_top(always_on_top)
        main_window.set_closable(closable)
        main_window.set_content_protection(enabled)
        main_window.set_cursor_grab(grab)
        main_window.set_cursor_icon(cursor)
        main_window.set_cursor_position(position)
        main_window.set_cursor_visible(visible)
        main_window.set_decorations(decorations)
        main_window.set_focus()
        main_window.set_fullscreen(fullscreen)
        main_window.set_ignore_cursor_events(ignore)
        main_window.set_ime_position(position)
        main_window.set_inner_size(size)
        main_window.set_inner_size_constraints(constraints)
        main_window.set_max_inner_size(max_size)
        main_window.set_maximizable(maximizable)
        main_window.set_min_inner_size(min_size)
        main_window.set_minimizable(minimizable)
        main_window.set_minimized(minimized)
        main_window.set_outer_position(position)
        main_window.set_progress_bar(progress)
        main_window.set_resizable(resizable)
        main_window.set_title(title)
        main_window.set_visible(visible)
        main_window.set_visible_on_all_workspaces(visible)
        main_window.set_window_icon(window_icon)
        main_window.set_enable(enabled)
        main_window.set_rtl(rtl)
        main_window.set_skip_taskbar(skip)
        main_window.set_taskbar_icon(taskbar_icon)
        main_window.set_undecorated_shadow(shadow)
        main_window.theme()
        main_window.title()
        main_window.window_handle()
        main_window.begin_resize_drag(edge, button, x, y)
        main_window.reset_dead_keys()

        */



        #[cfg(any(
            target_os = "windows",
            target_os = "macos",
            target_os = "ios",
            target_os = "android"
          ))]
          let mut webview_builder = WebViewBuilder::new(&main_window);
        
            
        
          #[cfg(not(any(
            target_os = "windows",
            target_os = "macos",
            target_os = "ios",
            target_os = "android"
          )))]
          let mut webview_builder = {
            use tao::platform::unix::WindowExtUnix;
            use wry::WebViewBuilderExtUnix;
            let vbox = window.default_vbox().unwrap();
            WebViewBuilder::new_gtk(vbox)
          };

        webview_builder.attrs = self.webview_core;
        let mywebview = webview_builder.build().unwrap();
 
        /*         
        mywebview.bounds();
        mywebview.clear_all_browsing_data();
        mywebview.close_devtools();
        mywebview.controller();
        mywebview.evaluate_script(js);
        mywebview.evaluate_script_with_callback(js, callback);
        mywebview.focus();
        mywebview.is_devtools_open();
        mywebview.load_url(url);
        mywebview.load_url_with_headers(url, headers);
        mywebview.open_devtools();
        mywebview.print();
        mywebview.reparent(hwnd);
        mywebview.set_background_color(background_color);
        mywebview.set_bounds(bounds);
        mywebview.set_memory_usage_level(level);
        mywebview.set_theme(theme);
        mywebview.set_visible(visible);
        mywebview.url();
        mywebview.zoom(scale_factor); 
        */
        

        self.webview = Some(mywebview);
        self.window = Some(main_window);


        
        main_window_loop(self.event_loop);
    }
}





pub(self) fn main_window_loop(
    event_loop: EventLoop<UserEvent>,  
    
) {

    event_loop.run(move |event, _, control_flow| {
        *control_flow = ControlFlow::Wait;

        match event {
            Event::WindowEvent {
                event: WindowEvent::CloseRequested,
                ..
            } => {
    
                *control_flow = ControlFlow::Exit;
            },
            _ => (),
        }
    });
}





fn main() {
    let mut app = Nexium::new();
    app.webview_builder_use_url("https://webkit.org/".to_string());
    app.webview_evaluate_script("console.log('Hallo')");
    app.run();
}





```
