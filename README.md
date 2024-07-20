# test_app



```rust

use std::{
    fs::read,
    path::{PathBuf, Path}};
use image::ImageFormat;
use mime_guess::from_path;
use tao::{
    dpi::{
        LogicalPosition, 
        LogicalSize, 
        LogicalUnit, 
        PhysicalPosition, 
        PhysicalSize, 
        PhysicalUnit, 
        PixelUnit, 
        Position, 
        Size
    }, event::{self, Event, WindowEvent}, event_loop::{ControlFlow, EventLoop, EventLoopBuilder}, monitor::VideoMode, window::{
        Fullscreen, Icon, Theme, WindowBuilder, WindowId, WindowSizeConstraints
    }
};
use wry::{
    http::{
        header::CONTENT_TYPE, 
        HeaderMap, 
        Request, 
        Response
    }, 
    DragDropEvent, 
    PageLoadEvent, 
    ProxyConfig, 
    Rect, 
    WebContext, WebViewBuilder
};


enum UserEvent {
    CloseWindow(WindowId),
    NewTitle(WindowId, String),
    NewWindow,
  }

fn get_nexium_response_protocol(
    request: Request<Vec<u8>>,
    index_page: &str,
    root_path: &PathBuf
) -> Result<Response<Vec<u8>>, Box<dyn std::error::Error>> {
    let path = request.uri().path();
    // Read the file content from file path

    let path = if path == "/" {
        index_page
    } else {
        // Removing leading slash
        &path[1..]
    };
    let full_path = std::fs::canonicalize(root_path.join(path))?;
    let content = std::fs::read(&full_path)?;
    #[cfg(target_os = "windows")]
    let headers = "https://wry.localhost".to_string();
    #[cfg(not(target_os = "windows"))]
    let headers = "wry://localhost".to_string();
    // Determine MIME type using `mime_guess`
    let mime_type = from_path(&full_path).first_or_octet_stream();

    Response::builder()
        .header(CONTENT_TYPE, mime_type.as_ref())
        .header("Access-Control-Allow-Origin", headers)
        .header("Cache-Control", "no-cache, no-store, must-revalidate")
        .header("Pragma", "no-cache")
        .header("Expires", "0")
        .header("Accept-Encoding", "gzip, compress, br, deflate")
        .body(content)
        .map_err(Into::into)
}

struct Nexium{
    event_loop:EventLoop<UserEvent>,
    webview:wry::WebViewBuilder<'static>,
    window:tao::window::WindowBuilder,
    
}

impl Nexium {
    #[allow(dead_code)]
    pub fn new(self){}

    // WebViewBuilder Methods
    #[allow(dead_code)]
    pub fn use_html(self, html:String){
        self.webview.with_html(html);
    
    }


    #[allow(dead_code)]
    pub fn use_accept_first_mouse(self,accept_first_mouse:bool){
        self.webview.with_accept_first_mouse(accept_first_mouse);
    
    }


    #[allow(dead_code)]
    pub fn use_asynchronous_custom_protocol(self, name:String, func:String){
        self.webview.with_asynchronous_custom_protocol(name, move|_,_|{
            println!("{}",func)
        });
    
    }


    #[allow(dead_code)]
    pub fn use_autoplay(self,autoplay:bool){
        self.webview.with_autoplay(autoplay);
    
    }


    #[allow(dead_code)]
    pub fn use_back_forward_navigation_gestures(self,gesture:bool){
        self.webview.with_back_forward_navigation_gestures(gesture);
    
    }


    #[allow(dead_code)]
    pub fn use_background_color(self,r:u8,g:u8,b:u8,a:u8){
        self.webview.with_background_color((r, g, b, a));

    }


    #[allow(dead_code)]
    pub fn use_bounds(self, x: i32, y: i32, width: i32, height: i32, position_type: &str, size_type: &str) {
        let position = match position_type {
            "physical" => Position::Physical(PhysicalPosition::new(x, y)),
            "logical" => Position::Logical(LogicalPosition::new(x as f64, y as f64)),
            _ => {
                println!("Invalid position type provided. Use 'physical' or 'logical'.");
                return;
            }
        };
    
        let size = match size_type {
            "physical" => Size::Physical(PhysicalSize::new(width as u32, height as u32)),
            "logical" => Size::Logical(LogicalSize::new(width as f64, height as f64)),
            _ => {
                println!("Invalid size type provided. Use 'physical' or 'logical'.");
                return;
            }
        };
        self.webview.with_bounds(Rect {
            position,
            size
        });

    }


    #[allow(dead_code)]
    pub fn use_clipboard(self,clipboard:bool){
        self.webview.with_clipboard(clipboard);
    
    }


    #[allow(dead_code)]
    pub fn use_viewer(
        self,
        name: Option<String>,
        index: Option<String>,
        root_path: Option<String>
    ) {
        let path = PathBuf::from(root_path.as_deref().unwrap_or(""));
    
        if let Some(index_str) = index {
            if index_str.starts_with("http://") || index_str.starts_with("https://") {
                self.webview.with_url(index_str);
            } else if index_str.starts_with("<") {
                self.webview.with_html(index_str);
            } else if index_str.ends_with(".html") {
                if let Some(root_path_str) = root_path {
                    let root_path_exists = Path::new(&root_path_str).exists();
                    if root_path_exists {
                        self.webview.with_custom_protocol(
                            name.unwrap_or_default(),
                            move |request| {
                                match get_nexium_response_protocol(request, &index_str, &path) {
                                    Ok(response) => response.map(Into::into),
                                    Err(e) => Response::builder()
                                        .header(CONTENT_TYPE, "text/plain")
                                        .status(500)
                                        .body(e.to_string().into_bytes())
                                        .unwrap()
                                        .map(Into::into),
                                }
                            },
                        );
                    } else {
                        eprintln!("Error: root_path '{}' does not exist", root_path_str);
                    }
                } else {
                    eprintln!("Error: root_path is None");
                }
            }
        } else {
            eprintln!("Error: index is None");
        }
    }


    #[allow(dead_code)]
    pub fn use_devtools(self,devtools: bool){
        self.webview.with_devtools(devtools);
    
    }


    #[allow(dead_code)]
    pub fn use_document_title_changed_handler(self,func:String){
        self.webview.with_document_title_changed_handler(move|_|{
            println!("{}",func)
        });
    
    }


    #[allow(dead_code)]
    pub fn use_download_completed_handler(self, func: String) {
        self.webview.with_download_completed_handler(move |url: String, path: Option<PathBuf>, success: bool| {
            println!("{}, {:?}, {}, {}", url, path, success, func);
            // You can also use url, path, and success here if needed
        });
    
    }


    #[allow(dead_code)]
    pub fn use_download_started_handler(self, func: String, allow_all:bool) {
        self.webview.with_download_started_handler(move |url: String, path: &mut PathBuf| {
            println!("{}, {:?}, {}", url, path, func);
            // You can also use `url`, `path`, and `func` here as needed

            // Example: Allow all downloads
            allow_all
        });
    
    }


    #[allow(dead_code)]
    fn use_drag_drop_handler(self, allow_all:bool) {
        self.webview.with_drag_drop_handler(move |event: DragDropEvent| {
            match event {
                DragDropEvent::Enter { paths, position } => {
                    println!("Drag entered with paths: {:?} at position: {:?}", paths, position);
                    // Handle the paths and position as needed
                }
                DragDropEvent::Leave {..} => {
                    println!("Leave");
                    // Handle the paths and position as needed
                }
                DragDropEvent::Over { position } => {
                    println!("Drag over with  position: {:?}",position);
                    // Handle the paths and position as needed
                }
                DragDropEvent::Drop { paths, position } => {
                    println!("Drag dropped with paths: {:?} at position: {:?}", paths, position);
                    // Handle the paths and position as needed
                }
           
                _ => (),
            }

            // Example: Allow all drag and drop events
            allow_all
        });
    
    }


    #[allow(dead_code)]
    pub fn use_focused(self, focused: bool){
        self.webview.with_focused(focused);
    
    }


    #[allow(dead_code)]
    pub fn use_headers(self,headers:HeaderMap){
        self.webview.with_headers(headers);
    
    }


    #[allow(dead_code)]
    pub fn use_hotkeys_zoom(self, zoom: bool){
        self.webview.with_hotkeys_zoom(zoom);
    
    }


    #[allow(dead_code)]
    pub fn use_incognito(self,incognito:bool){
        self.webview.with_incognito(incognito);
    
    }


    #[allow(dead_code)]
    pub fn use_initialization_script(self,js_code:&str){
        self.webview.with_initialization_script(js_code);
    
    }


    #[allow(dead_code)]
    pub fn use_ipc_handler(self){
        self.webview.with_ipc_handler(move|_|{});
    
    }


    #[allow(dead_code)]
    pub fn use_navigation_handler(self, all_navigation:bool){
        self.webview.with_navigation_handler(move|url: String| {
            println!("Navigating to: {}", url);
            all_navigation
        });
    
    }


    #[allow(dead_code)]
    pub fn use_new_window_req_handler(self,all_navigation:bool){
        self.webview.with_new_window_req_handler(move|url: String| {
            println!("Navigating to: {}", url);
            all_navigation
        });
    
    }


    #[allow(dead_code)]
    pub fn use_on_page_load_handler(self) {
        self.webview.with_on_page_load_handler(move |event: PageLoadEvent, url: String| {
            match event {
                PageLoadEvent::Started => println!("Page load started at: {}", url),
                PageLoadEvent::Finished => println!("Page load finished at: {}", url),
                // If there are other variants, handle them accordingly
            }
        });
    
    }


    #[allow(dead_code)]
    pub fn use_proxy_config(self, proxy_config:ProxyConfig){
        self.webview.with_proxy_config(proxy_config);
    
    }


    #[allow(dead_code)]
    pub fn use_transparent(self, transparent:bool){
        self.webview.with_transparent(transparent);
    
    }


    #[allow(dead_code)]
    pub fn use_url(self, url:String){
        self.webview.with_url(url);
    
    }


    #[allow(dead_code)]
    pub fn use_url_and_headers(self,url:String, header:HeaderMap){
        self.webview.with_url_and_headers(url, header);
    
    }


    #[allow(dead_code)]
    pub fn use_user_agent(self, user_agen:String){
        self.webview.with_user_agent(&user_agen);
    
    }


    #[allow(dead_code)]
    pub fn use_visible(self, visible:bool){
        self.webview.with_visible(visible);
    
    }


    #[allow(dead_code)]
    pub fn use_web_context(self, web_context:& mut WebContext){
        self.webview.with_web_context(web_context);
    
    }


    #[allow(dead_code)]
 
    // WindowBuilder Methods
    pub fn use_always_on_bottom(self,always_on_bottom: bool){
        self.window.with_always_on_bottom(always_on_bottom);
    
    }


    #[allow(dead_code)]
    pub fn use_always_on_top(self,always_on_top:bool){
        self.window.with_always_on_top(always_on_top);
    
    }


    #[allow(dead_code)]
    pub fn use_closable(self,closable:bool){
        self.window.with_closable(closable);
    
    }


    #[allow(dead_code)]
    pub fn use_content_protection(self,protected:bool){
        self.window.with_content_protection(protected);
    
    }


    #[allow(dead_code)]
    pub fn use_decorations(self,decorations:bool){
        self.window.with_decorations(decorations);
    
    }


    #[allow(dead_code)]
    pub fn use_fullscreen(self,fullscreen_type:&str, video_mode:VideoMode){
        let fullscreen = match fullscreen_type {
            "exclusive" => Fullscreen::Exclusive(video_mode),
            "borderless" => Fullscreen::Borderless(None),
           
            _ => {
                println!("Invalid fullscreen type provided. Use 'exclusive' or 'borderless'.");
                return;
            }
        };
        self.window.with_fullscreen(Some(fullscreen));
    
    }


    #[allow(dead_code)]
    pub fn use_inner_size(self,width:i32,height:i32, size_type:&str){
        let size = match size_type {
            "physical" => Size::Physical(PhysicalSize::new(width as u32, height as u32)),
            "logical" => Size::Logical(LogicalSize::new(width as f64, height as f64)),
            _ => {
                println!("Invalid size type provided. Use 'physical' or 'logical'.");
                return;
            }
        };
        self.window.with_inner_size(size);
    
    }


    #[allow(dead_code)]
    pub fn use_inner_size_constraints(
        self,
        min_width: i32,
        min_height: i32,
        max_width: i32,
        max_height: i32,
        constraints_type: &str
    ) {
        let (min_width, min_height, max_width, max_height) = match constraints_type {
            "logical" => (
                PixelUnit::Logical(LogicalUnit(min_width as f64)),
                PixelUnit::Logical(LogicalUnit(min_height as  f64)),
                PixelUnit::Logical(LogicalUnit(max_width as f64)),
                PixelUnit::Logical(LogicalUnit(max_height as f64)),
            ),
            "physical" => (
                PixelUnit::Physical(PhysicalUnit(min_width)),
                PixelUnit::Physical(PhysicalUnit(min_height)),
                PixelUnit::Physical(PhysicalUnit(max_width)),
                PixelUnit::Physical(PhysicalUnit(max_height)),
            ),
            _ => {
                eprintln!("Error: Invalid constraints_type '{}'", constraints_type);
                return; // Early return on invalid constraints_type
            }
        };
    
        self.window.with_inner_size_constraints(WindowSizeConstraints::new(
            Some(min_width), Some(min_height), Some(max_width), Some(max_height)
        ));
    
    }


    #[allow(dead_code)]
    pub fn use_max_inner_size(self,width:i32,height:i32, size_type:&str){
        let size = match size_type {
            "physical" => Size::Physical(PhysicalSize::new(width as u32, height as u32)),
            "logical" => Size::Logical(LogicalSize::new(width as f64, height as f64)),
            _ => {
                println!("Invalid size type provided. Use 'physical' or 'logical'.");
                return;
            }
        };
        self.window.with_max_inner_size(size);
    
    }


    #[allow(dead_code)]
    pub fn use_maximizable(self,maximizable: bool){
        self.window.with_maximizable(maximizable);
    
    }


    #[allow(dead_code)]
    pub fn use_maximized(self,maximized: bool){
        self.window.with_maximized(maximized);
    
    }


    #[allow(dead_code)]
    pub fn use_min_inner_size(self,width:i32,height:i32, min_size_type:&str){
        let size = match min_size_type {
            "physical" => Size::Physical(PhysicalSize::new(width as u32, height as u32)),
            "logical" => Size::Logical(LogicalSize::new(width as f64, height as f64)),
            _ => {
                println!("Invalid size type provided. Use 'physical' or 'logical'.");
                return;
            }
        };
        self.window.with_min_inner_size(size);
    
    }


    #[allow(dead_code)]
    pub fn use_minimizable(self,minimizable: bool){
        self.window.with_minimizable(minimizable);
    
    }


    #[allow(dead_code)]
    pub fn use_position(self, x:i32, y:i32, position_type:&str){
        let position = match position_type {
            "physical" => Position::Physical(PhysicalPosition::new(x, y)),
            "logical" => Position::Logical(LogicalPosition::new(x as f64, y as f64)),
            _ => {
                println!("Invalid position type provided. Use 'physical' or 'logical'.");
                return;
            }
        };
        self.window.with_position(position);
    
    }


    #[allow(dead_code)]
    pub fn use_resizable(self,resizable: bool){
        self.window.with_resizable(resizable);
    
    }


    #[allow(dead_code)]
    pub fn use_theme(self,theme_type:&str){
        let theme = match theme_type {
            "Light" => Theme::Light,
            "dark" => Theme::Dark,
            "system" => Theme::default(),
            _ => {
                println!("Invalid theme type provided. Use 'Light' or 'dark' or 'system'.");
                return;
            }
        };
        self.window.with_theme(Some(theme));
    
    }


    #[allow(dead_code)]
    pub fn use_title(self,title:&str){
        self.window.with_title(title);
    
    }


    #[allow(dead_code)]
    pub fn use_visible_on_all_workspaces(self,visible: bool){
        self.window.with_visible_on_all_workspaces(visible);
    
    }


    #[allow(dead_code)]
    pub fn use_window_icon(self,icon:&str){
        let icon_object = match read(icon) {
            Err(_) => None,
            Ok(bytes) => {
                let imagebuffer =
                    match image::load_from_memory_with_format(&bytes, ImageFormat::Png) {
                        Err(_) => None,
                        Ok(loaded) => {
                            let imagebuffer = loaded.to_rgba8();
                            let (icon_width, icon_height) = imagebuffer.dimensions();
                            let icon_rgba = imagebuffer.into_raw();
                            match Icon::from_rgba(icon_rgba, icon_width, icon_height) {
                                Err(_) => None,
                                Ok(icon) => Some(icon),
                            }
                        }
                    };
                imagebuffer
            }
        };
        self.window.with_window_icon(icon_object);
    
    }


    #[allow(dead_code)]
    pub fn on_applications_startup_event(self, _func:String){}

    #[allow(dead_code)]
    pub fn on_applications_shutdown_event(self, _func:String){}

    #[allow(dead_code)]
    pub fn run(mut self, _func: String) {
        self.event_loop = EventLoopBuilder::<UserEvent>::with_user_event().build();
        let _proxy = self.event_loop.create_proxy();
        self.window = WindowBuilder::new().build(&self.event_loop).unwrap();

        #[cfg(any(
          target_os = "windows",
          target_os = "macos",
          target_os = "ios",
          target_os = "android"
        ))]
        {
            self.webview = WebViewBuilder::new(&self.window)
                .build()
                .unwrap();
        }

        #[cfg(not(any(
          target_os = "windows",
          target_os = "macos",
          target_os = "ios",
          target_os = "android"
        )))]
        {
            use tao::platform::unix::WindowExtUnix;
            use wry::application::platform::unix::WebViewBuilderExtUnix;
            let vbox = self.window.default_vbox().unwrap();
            self.webview = WebViewBuilder::new_gtk(vbox)
                .build()
                .unwrap();
        }

        self.event_loop.run(move |event, _, control_flow| {
            *control_flow = ControlFlow::Wait;

            if let Event::WindowEvent {
                event: WindowEvent::CloseRequested,
                ..
            } = event
            {
                *control_flow = ControlFlow::Exit;
            }
        });
    }
}




fn main() {
    println!("Hello, world!");
}

```
