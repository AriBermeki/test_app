


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
    event::{Event, WindowEvent}, 
    event_loop::{ControlFlow, EventLoop, EventLoopBuilder, EventLoopProxy}, 
    window::{
        Fullscreen, Icon, Theme, Window, WindowAttributes, WindowBuilder, WindowSizeConstraints,
    },
};

use tao::window::WindowId;



pub(crate) enum NexiumEventAPI {
    CloseWindow(WindowId),
    NewTitle(WindowId, String),
    NewWindow,
  }



struct NexiumWebView{
    webview_attrs:WebViewAttributes,
    
}


impl NexiumWebView {
    pub fn new()->NexiumWebView{
        Self{
            webview_attrs:Default::default(),
         
        }
    }

    pub fn with_accept_first_mouse(&mut self){}
    pub fn with_asynchronous_custom_protocol(&mut self){}
    pub fn with_autoplay(&mut self){}
    pub fn with_back_forward_navigation_gestures(&mut self){}
    pub fn with_background_color(&mut self){}
    pub fn with_bounds(&mut self){}
    pub fn with_clipboard(&mut self){}
    pub fn with_custom_protocol(&mut self){}
    pub fn with_devtools(&mut self){}
    pub fn with_document_title_changed_handler(&mut self){}
    pub fn with_download_completed_handler(&mut self){}
    pub fn with_download_started_handler(&mut self){}
    pub fn with_drag_drop_handler(&mut self){}
    pub fn with_focused(&mut self){}
    pub fn with_headers(&mut self){}
    pub fn with_hotkeys_zoom(&mut self){}
    pub fn with_html(&mut self){}
    pub fn with_incognito(&mut self){}
    pub fn with_initialization_script(&mut self){}
    pub fn with_ipc_handler(&mut self){}
    pub fn with_navigation_handler(&mut self){}
    pub fn with_new_window_req_handler(&mut self){}
    pub fn with_on_page_load_handler(&mut self){}
    pub fn with_proxy_config(&mut self){}
    pub fn with_transparent(&mut self){}
    pub fn with_url(&mut self){}
    pub fn with_url_and_headers(&mut self){}
    pub fn with_user_agent(&mut self){}
    pub fn with_visible(&mut self){}
    pub fn with_web_context(&mut self){}


    pub fn get_attributes(&self) -> &WebViewAttributes {
        &self.webview_attrs
    }



}











struct NexiumWindow{
    window_attrs:WindowAttributes,
   
}


impl NexiumWindow {
    pub fn new()->NexiumWindow{
        Self{
            window_attrs:Default::default(),
       
        }
    }


    
    pub fn with_always_on_bottom(&mut self){}
    pub fn with_always_on_top(&mut self){}
    pub fn with_closable(&mut self){}
    pub fn with_content_protection(&mut self){}
    pub fn with_decorations(&mut self){}
    pub fn with_focused(&mut self){}
    pub fn with_fullscreen(&mut self){}
    pub fn with_inner_size(&mut self){}
    pub fn with_inner_size_constraints(&mut self){}
    pub fn with_max_inner_size(&mut self){}
    pub fn with_maximizable(&mut self){}
    pub fn with_maximized(&mut self){}
    pub fn with_min_inner_size(&mut self){}
    pub fn with_minimizable(&mut self){}
    pub fn with_position(&mut self){}
    pub fn with_resizable(&mut self){}
    pub fn with_theme(&mut self){}
    pub fn with_title(&mut self){}
    pub fn with_transparent(&mut self){}
    pub fn with_visible(&mut self){}
    pub fn with_visible_on_all_workspaces(&mut self){}
    pub fn with_window_icon(&mut self){}

    pub fn get_attributes(&self) -> &WindowAttributes {
        &self.window_attrs
    }


}






struct Nexium{
    event_loop: EventLoop<NexiumEventAPI>,
    window:Window,
    webview:WebView,
   
}


impl Nexium {
    pub fn new(window_attrabiute:NexiumWindow, webview_attrabiute:NexiumWebView)->Nexium{
        let event_loop = EventLoopBuilder::<NexiumEventAPI>::with_user_event().build();

        let mut window_builder = WindowBuilder::new();

        window_builder.window = window_attrabiute.get_attributes().clone();

        let main_window = window_builder.build(&event_loop).unwrap();

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
        let attributes = webview_attrabiute.get_attributes();
        webview_builder.attrs = attributes;
        let main_webview = webview_builder.build().unwrap();

        Self{
            event_loop:event_loop,
            window:main_window,
            webview:main_webview
       
        }
    }


    /// Window Struct Methods
    pub fn available_monitors(&mut self){}
    pub fn current_monitor(&mut self){}
    pub fn cursor_position(&mut self){}
    pub fn drag_resize_window(&mut self){}
    pub fn drag_window(&mut self){}
    pub fn fullscreen(&mut self){}
    pub fn id(&mut self){}
    pub fn inner_position(&mut self){}
    pub fn inner_size(&mut self){}
    pub fn is_closable(&mut self){}
    pub fn is_decorated(&mut self){}
    pub fn is_focused(&mut self){}
    pub fn is_maximizable(&mut self){}
    pub fn is_maximized(&mut self){}
    pub fn is_minimizable(&mut self){}
    pub fn is_minimized(&mut self){}
    pub fn is_resizable(&mut self){}
    pub fn is_visible(&mut self){}
    pub fn monitor_from_point(&mut self){}
    pub fn outer_position(&mut self){}
    pub fn outer_size(&mut self){}
    pub fn primary_monitor(&mut self){}
    pub fn request_redraw(&mut self){}
    pub fn request_user_attention(&mut self){}
    pub fn scale_factor(&mut self){}
    pub fn set_always_on_bottom(&mut self){}
    pub fn set_always_on_top(&mut self){}
    pub fn set_closable(&mut self){}
    pub fn set_content_protection(&mut self){}
    pub fn set_cursor_grab(&mut self){}
    pub fn set_cursor_icon(&mut self){}
    pub fn set_cursor_position(&mut self){}
    pub fn set_cursor_visible(&mut self){}
    pub fn set_decorations(&mut self){}
    pub fn set_focus(&mut self){}
    pub fn set_fullscreen(&mut self){}
    pub fn set_ignore_cursor_events(&mut self){}
    pub fn set_ime_position(&mut self){}
    pub fn set_inner_size(&mut self){}
    pub fn set_inner_size_constraints(&mut self){}
    pub fn set_max_inner_size(&mut self){}
    pub fn set_maximizable(&mut self){}
    pub fn set_maximized(&mut self){}
    pub fn set_min_inner_size(&mut self){}
    pub fn set_minimizable(&mut self){}
    pub fn set_minimized(&mut self){}
    pub fn set_outer_position(&mut self){}
    pub fn set_progress_bar(&mut self){}
    pub fn set_resizable(&mut self){}
    pub fn set_title(&mut self){}
    pub fn set_window_visible(&mut self){}
    pub fn set_visible_on_all_workspaces(&mut self){}
    pub fn set_window_icon(&mut self){}
    pub fn theme(&mut self){}
    pub fn title(&mut self){}


    /// WebView Struct Methods
    pub fn bounds(&mut self){}
    pub fn clear_all_browsing_data(&mut self){}
    pub fn close_devtools(&mut self){}
    pub fn controller(&mut self){}
    pub fn evaluate_script(&mut self){}
    pub fn evaluate_script_with_callback(&mut self){}
    pub fn focus(&mut self){}
    pub fn is_devtools_open(&mut self){}
    pub fn load_url(&mut self){}
    pub fn load_url_with_headers(&mut self){}
    pub fn open_devtools(&mut self){}
    pub fn print(&mut self){}
    pub fn reparent(&mut self){}
    pub fn set_background_color(&mut self){}
    pub fn set_bounds(&mut self){}
    pub fn set_memory_usage_level(&mut self){}
    pub fn set_theme(&mut self){}
    pub fn set_webView_visible(&mut self){}
    pub fn url(&mut self){}
    pub fn zoom(&mut self){}

    pub fn run(self){
        self.event_loop.run(move |event, _, control_flow| {
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



}






fn main(){

}

```
