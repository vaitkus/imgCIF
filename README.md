# A CIF dictionary for raw image data

The imgCIF dictionary provides 
data names for description of raw image data, usually from diffraction experiments. 
Currently the main development of imgCIF takes place
in the [cbflib repository](https://github.com/yayahjb/cbflib/tree/master/doc).

This repository holds a DDLm version of the imgCIF dictionary found in the
main branch of that repository in this repository's main branch. This DDLm
version may be used as a data name reference.

# Deriving the DDLm version from the DDL2 version

The DDLm version was automatically obtained from the DDL2 version by running the
command:

```
julia ddl_to_ddl.jl -c cif_core.dic -i -k _diffrn.id -t "Identifier for data collection." -o cif_img.dic cif_img_1.8.4.dic ddl2
```

using the `ddl_to_ddl.jl` tool and associated translation dictionaries from the
`https://github.com/jamesrhester/ddl_to_ddl` repository and the `cif_core.dic`
found in the COMCIFS `cif_core` repository.

In particular, the translation options above perform the following:

1. All DDLm attributes are "calculated" from DDL2 attributes according to
translation dictionaries included in the `ddl_to_ddl` repository.

2. Additional non-canonical data attributes (prefixed with `ddl2`)
are included in the translated DDLm dictionary to preserve information not
captured by DDLm attributes. For details see the `ddl_to_ddl` tool.

3. Any common categories defined to be of class `Set` in `cif_core.dic` are
restated to be `Set` categories in the translated dictionary

4. All categories containing the string `diffrn` in their name have a child
data name of `_diffrn.id` added to their key data names in keeping with the 
implicit DDL2 convention.

5. The `cif_core` dictionary is imported by the dictionary to show that these
definitions build on those found in `cif_core`.

Finally, the result is pretty-printed and manual layout adjustments performed
to meet the style guide requirements.
