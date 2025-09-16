# A CIF dictionary for raw image data

The imgCIF dictionary provides 
data names for description of raw image data, usually from diffraction experiments. 
Currently the main development of imgCIF takes place
in the [cbflib repository](https://github.com/yayahjb/cbf_imgcif_dictionary).

This repository holds a DDLm version of the imgCIF dictionary found in the
main branch of that repository in this repository's main branch. This DDLm
version may be used as a data name reference.

## Deriving the DDLm version from the DDL2 version

The DDLm version was automatically obtained from the DDL2 version by running the
command:

```
julia ddl_to_ddl.jl -c multi_block_core.dic -i --short --strict -t CIF_IMG -o cif_img.dic cif_img_1.8.9.1.dic ddl2
```

using the `ddl_to_ddl.jl` tool and associated translation dictionaries from the
`https://github.com/jamesrhester/ddl_to_ddl` repository and the `multi_block_core.dic`
found in the [COMCIFS `MultiBlock_Dictionary` repository](https://github.com/COMCIFS/MultiBlock_Dictionary).

In particular, the translation options above perform the following:

1. All DDLm attributes are "calculated" from DDL2 attributes according to
translation dictionaries included in the `ddl_to_ddl` repository.

2. Additional non-canonical data attributes (prefixed with `ddl2`)
are included in the translated DDLm dictionary to preserve information not
captured by DDLm attributes. For details see the `ddl_to_ddl` tool.

3. Any common categories defined to be of class `Set` in `cif_core.dic` are
restated to be `Set` categories in the translated dictionary

4. The dictionary title is changed to `cif_img` to conform to DDLm style.

5. (`--short`): The import of the MultiBlock dictionary will not specify a directory for simplicity
in testing. This implies that all dictionaries are in the same directory.

6. (`--strict`): Modifications to satisfy DDLm style and strict semantic requirements:
    1. All DDL2-specific categories generated at (2) are dropped
    2. Data names defined as aliases have any separate definition blocks removed
    3. Version numbers enhanced to conform to semantic versioning
    4. Version numbers re-ordered to be ascending order
    5. Aliases defined in both `cif_img.dic` and the imported dictionary are removed from
       `cif_img.dic`

## Rederiving the DDL2 version

`cif_img_expanded.dic` is generated as above, but without the `--strict` and `-t` options. The
`ddl2` version can be recovered by running

```
julia ddl_to_ddl.jl -o cif_img_ddl2.dic cif_img_expanded.dic
```
