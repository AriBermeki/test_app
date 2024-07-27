



```rust


use pyo3::{exceptions::PyRuntimeError, prelude::*};
use pyo3::types::PyDict;
mod app;
use crate::app::Nexium;

#[pyclass]
pub struct PyNexium {
    window: Option<Nexium>,
}

#[pymethods]
impl PyNexium {
    #[new]
    pub fn new() -> PyNexium {
        let main_window = Nexium::new();
        PyNexium { window: Some(main_window) }
    }

    pub fn start(&mut self, py: Python, json_data: &str, on_startup_event: Option<PyObject>, on_shutdown_event: Option<PyObject>) -> PyResult<()> {
        let gil = Python::acquire_gil();
        let py = gil.python();

        // Call the on_startup_event if it's provided
        if let Some(ref startup_event) = on_startup_event {
            startup_event.call0(py).map_err(|e| {
                PyErr::new::<PyRuntimeError, _>(format!("Error in on_startup_event: {:?}", e))
            })?;
        }

        if let Some(win) = self.window.take() {
            win.run(json_data.into());

            // Call the on_shutdown_event if it's provided
            if let Some(ref shutdown_event) = on_shutdown_event {
                shutdown_event.call0(py).map_err(|e| {
                    PyErr::new::<PyRuntimeError, _>(format!("Error in on_shutdown_event: {:?}", e))
                })?;
            }
            
            Ok(())
        } else {
            Err(PyRuntimeError::new_err("already started"))
        }
    }
}

#[pymodule]
fn pycore(py: Python, m: &PyModule) -> PyResult<()> {
    m.add_class::<PyNexium>()?;
    Ok(())
}




```