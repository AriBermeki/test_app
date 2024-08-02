```rust

 use tao::{
    event_loop::{EventLoop, EventLoopBuilder},
    window::{Window, WindowAttributes, WindowBuilder},
};
use wry::{WebView, WebViewAttributes, WebViewBuilder};

pub enum PyframeEventAPI {}

struct WindowFrame {
    win_attrs: WindowAttributes,
}

impl WindowFrame {
    pub fn new() -> WindowFrame {
        Self {
            win_attrs: Default::default(),
        }
    }
}

struct WebViewFrame {
    webview_attrs: WebViewAttributes,
}

impl WebViewFrame {
    pub fn new() -> WebViewFrame {
        Self {
            webview_attrs: Default::default(),
        }
    }
    pub fn get_webview_attrs(&mut self)->&WebViewAttributes{
        &self.webview_attrs
    }
}

// Implementing Clone for WebViewFrame
impl Clone for WebViewFrame {
    fn clone(&self) -> Self {
        Self {
            webview_attrs: self.webview_attrs.clone(), // error  no method named `clone` found for struct `WebViewAttributes` in the current scope method not found in `WebViewAttributes`
        }
    }
}
 
 struct PyFrame {
     event_loop: EventLoop<PyframeEventAPI>,
     window: Window,
     webview: WebView,
 }
 
 impl PyFrame {

    pub fn new(
        window_attribute:&WindowFrame,
        webview_attribute:&WebViewFrame,
        _web_context: Option<bool>,
    ) -> PyFrame {
        
        let event_loop = EventLoopBuilder::<PyframeEventAPI>::with_user_event().build();
        let mut window_builder = WindowBuilder::new();

        // We need the Python class instance to set the WindowBuilder methods
      
        window_builder.window = window_attribute.win_attrs.clone();

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
            let vbox = main_window.default_vbox().unwrap();
            WebViewBuilder::new_gtk(vbox)
        };
    // Set webview attributes
    let attributes = &webview_attribute.webview_attrs;









    webview_builder.attrs = attributes.clone();
    let main_webview = webview_builder.build().unwrap();

        Self {
            event_loop: event_loop,
            window: main_window,
            webview: main_webview,
        }
    }
}




```
