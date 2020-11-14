# Código

Puedes aplicar el archivo `holguin.patch` sobre la rama master
del repositorio `cpython/`.
El último commit del repositorio cuando realicé los cambios era:
`da6f098188` en caso que al aplicar el parche tengas conflictos.

Antes de agregarlo puedes hacer una verificación con:
`git apply --check holguin.patch` y luego para aplicarlo un
`git apply holguin.patch`.

## La Magia

La primera modificación que quizás notaste, fue la broma de decir
que era Python 4.0, para ello, solo modifiqué en el archivo
`Include/patchlevel.h` el macro `PY_VERSION`:

```c
 /* Version as a string */
-#define PY_VERSION              "3.10.0a1+"
+#define PY_VERSION              "4.00.0a1+"
 /*--end constants--*/
```

Lo que automaticamente cambia el mensaje cuando lo ejecutamos.

Las otras modificaciones al código fuente, son principalmente agregar nuevos
métodos al tipo `list` de Python.

Por ejemplo podemos mirar el caso de `list.appendappend()`:

En la cabecera:

```c
PyDoc_STRVAR(list_appendappend__doc__,
"appendappend($self, object, /)\n"
"--\n"
"\n"
"Append object to the end of the list, twice...doh!.");

#define LIST_APPENDAPPEND_METHODDEF    \
    {"appendappend", (PyCFunction)list_appendappend, METH_O, list_appendappend__doc__},
```

La implementación:

```c
/*[clinic input]
list.appendappend

     object: object
     /

Append object to the end of the list, twice...doh!.
[clinic start generated code]*/

static PyObject *
list_appendappend(PyListObject *self, PyObject *object)
{
    // El método append normal solo utiliza una llamada
    // pero agregamos una segunda despuesdel '&&'
    if (app1(self, object) == 0 && app1(self, object) == 0)
        Py_RETURN_NONE;
    return NULL;
}
```

y luego en la lista de métodos del objeto, agregamos la definición
de la cabecera:

```c
static PyMethodDef list_methods[] = {
    ...
    LIST_APPENDAPPEND_METHODDEF
    ...
```

Lo mismo ocurre con los otros casos, mezclando algunas funciones
utilitarias o programas para Linux como `cmatrix` y `sl`.

Puedes mirar el parche para entender las funciones:
* `list.dneppa`,
* `list.ask`,
* `list.train`,
* `list.matrix`, y
* `list.receta`.
