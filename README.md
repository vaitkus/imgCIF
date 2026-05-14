[![CIF dictionary check](https://github.com/COMCIFS/imgCIF/actions/workflows/main.yml/badge.svg)](https://github.com/COMCIFS/imgCIF/actions)

# A CIF dictionary for raw image data

The imgCIF dictionary provides 
data names for description of raw image data, usually from diffraction experiments. 
Currently the main development of imgCIF takes place
in the [wwPDB repository](https://github.com/wwpdb-dictionaries/cbf_imgcif_dictionary).

The present repository holds a DDLm version of the DDL2 imgCIF dictionary found in the
main branch of that repository. The DDLm version here may be used as a data name reference
for DDLm-based work and as an imported dictionary for DDLm dictionaries that interact with
image-based data.

## Caveats and Considerations

Ideally, DDL2 and DDLm dictionaries can be used interchangeably as a reference
when reading and writing data names (the data names themselves are
identical). The following caveats to this ideal situation currently apply:

1. The DDLm imgCIF dictionary inherits `DIFFRN`-linked
categories from the [multi-block dictionary](https://github.com/COMCIFS/cif_multiblock)
rather than [PDBx/mmCIF](https://mmcif.wwpdb.org). That is,
unlike mmCIF, `DIFFRN_DETECTOR`, `DIFFRN_RADIATION` and `DIFFRN_MEASUREMENT` do
*not* include a data name linked to `_diffrn.id` as a key data name. An issue
will only arise where multiple `_diffrn.id`s form part of a single dataset, which is
rare in the macromolecular context. In the case that there are multiple
`_diffrn.id`s, data for each `_diffrn.id` should be presented in separate data
blocks where DDLm dictionary tools are used, and merged into a single data block where
DDL2-based tools are used. In
addition, DDLm requires that identical items (detectors/sources/goniometers) in
the above categories should be
named identically for each `_diffrn.id`.

2. `cif_img_variant.dic` includes the `VARIANT` category. Data names in this category
are currently excluded from the main PDBx/mmCIF dictionary and so have been separated
out here as well. The `VARIANT` category in this dictionary does not include `_diffrn.id`
or `_entry.id` as key data names.

3. `expanded/cif_img_expanded.dic` includes additional non-DDLm attributes in some data
name definitions to permit round-tripping back to DDL2 (see below). It is not suitable
as a DDLm dictionary import target or for use with automated tools, but is useful as a
human-readable reference for additional information, e.g. regular expressions that
certain data name values should match.

## Deriving the DDLm version from the DDL2 version

The DDLm version was automatically obtained from the DDL2 version by running the following
commands:

```
julia ddl_to_ddl.jl -c multi_block_core.dic -i --short --strict -t CIF_IMG -o cif_img.dic cbf_imgcif_dictionary/ddl2/cif_img.dic ddl2
```

using the `ddl_to_ddl.jl` and `remove_category.jl` tools and associated DDL
translation dictionaries from the `https://github.com/jamesrhester/ddl_to_ddl`
repository and the `multi_block_core.dic` found in the [COMCIFS Multi-Block
dictionary repository](https://github.com/COMCIFS/cif_multiblock).  A [COMCIFS
fork of the DDL2 imgCIF
dictionary](https://github.com/COMCIFS/cbf_imgcif_dictionary) was used as the
DDL2 dictionary source.  Currently this fork contains some corrections for
minor errors in the original.

The `ddl_to_ddl.jl` command above does the following:

1. All DDLm attributes are "calculated" from DDL2 attributes according to
translation dictionaries included in the `ddl_to_ddl` repository. Additional
non-canonical data attributes (prefixed with `ddl2`) are included in the
translated DDLm dictionary to preserve information not captured by DDLm
attributes. For details see the `ddl_to_ddl` tool. The additional attributes
are later dropped (see below).

2. Any common categories defined to be of class `Set` in `multi_block_core.dic` are
restated to be `Set` categories in the translated dictionary

3. The dictionary title is changed to `cif_img` to conform to DDLm style.

4. (`--short`): The import of `multi_block_core.dic` in the emitted DDLm
dictionary will not specify a directory for simplicity in testing. This implies
that all dictionaries are in the same directory.

5. (`--strict`): Modifications to satisfy DDLm style and strict semantic requirements:
    1. All DDL2-specific categories generated at (2) are dropped
    2. Data names defined as aliases have any separate definition blocks removed
    3. Version numbers enhanced to conform to semantic versioning
    4. Version numbers re-ordered to be ascending order
    5. Aliases defined in both `cif_img.dic` and the imported dictionary are removed from
       `cif_img.dic`

```
julia remove_category.jl -s -k _variant.variant cif_img.dic variant
```

The `remove_category.jl` command creates `cif_img_variant.dic` and alters `cif_img.dic` as follows:

1. All data names linked to `_variant.variant` are moved to the new dictionary
2. The category `variant` is moved to the new dictionary
3. Mentions of `variant`-type data names and the `variant` category are removed
from the dictionary description
4. The new dictionary makes `VARIANT` a `Set` category with single key data name
`_variant.variant`. This means that alternate variants by default should be presented in
separate data blocks.

## Rederiving the DDL2 version

`expanded/cif_img_expanded.dic` is generated as above, but without the `--strict` and `-t` options. The
`ddl2` version can be recovered by running

```
julia ddl_to_ddl.jl -o cif_img_ddl2.dic expanded/cif_img_expanded.dic ddlm
```
