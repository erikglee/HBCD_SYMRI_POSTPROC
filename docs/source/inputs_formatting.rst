.. HBCD_SYMRI_POSTPROC documentation master file, created by
   sphinx-quickstart on Wed Jun  5 10:48:12 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Expected Formatting of Inputs
=============================

BIDS directory
--------------

The BIDS directory should have exactly one T1w and/or T2w image
for each session that will be processed. The T1w and/or T2w image
should already be registered to the segmentation(s) found in the
segmentations directory. If both T1w/T2w images and segmentations
are present, the pipeline will currently (as of 6/5/24) give priority
to registering the relaxometry images to the T2w images.

SyMRI/relaxometry directory
---------------------------

The SyMRI directory should have at a minimum a T1, T2, and PD map in compliance
with the following naming structure: ::

   /relaxometry_dir/sub-{label}/ses-{label}/anat/sub-{label}_ses-{label}*T1map.nii.gz
   /relaxometry_dir/sub-{label}/ses-{label}/anat/sub-{label}_ses-{label}*T2map.nii.gz
   /relaxometry_dir/sub-{label}/ses-{label}/anat/sub-{label}_ses-{label}*PDmap.nii.gz

There must only be one T1, T2, and PD map per session, but there may be additional files
in the directory that are not used by the pipeline. In HBCD these files are generated by
SyMRI's relaxometry tool, but any tool can be used to generate these files. However in all
cases the images must have corresponding json files, which can be empty if there is no
associated metadata.

The primary requirement of th T1/T2/PD maps is that they are all registered to one another,
having the same dimensions and voxel sizes.

bibsnet_deriv_dir (i.e. segmentation directory)
-----------------------------------------------

The segmentations found under this directory should be of the
following form: ::
   
   /segmentation_dir/sub-{label}/ses-{label}/anat/sub-{label}_ses-{label}*space-{label}_desc-aseg_dseg.nii.gz
   /segmentation_dir/sub-{label}/ses-{label}/anat/sub-{label}_ses-{label}*space-{label}_desc-aseg_dseg.nii.gz
   
In the above formatting, the presence of a session folder is optional. The "*" icon denotes
flexibility in the naming convention at a certain point, and the final label entry following "space-"
must either be T1w or T2w. The first file described above is a segmentation with individual brain
regions, and the second file is a binary brain mask that is used to aid in the registration.

In HBCD both of these files are generated by BIBSNET, but any segmentations using the naming
convention above with voxel values that correspond to the FreeSurferColorLUT file can be used.
No matter what tool is used to generate the segmentations, the FreeSurferColorLUT will first be
loaded, then the segmentation will be loaded, and following a registration procedure any non-zero
value within the segmentation will be mapped to the corresponding label in the FreeSurferColorLUT.
If there is a voxel label that is not found in the FreeSurferColorLUT this will result in an error.


region_groupings_json
---------------------

This file is a json whose keys correspond to user-specified custom names for one
or more groupings of brain regions, and whose values are lists of the brain region
labels that are to be included in each grouping. The labels included in the list
must come directly from the "Label Name" column of the FreeSurfer colorlut. When
the tool is given a region_groupings_json, relaxometry statistics will be computed
within all voxels that correspond to a given group.

An example formatting is as follows: ::

   {
       "Cerebral-Cortex": ["Left-Cerebral-White-Matter", "Right-Cerebral-White-Matter"],
       "Cerebral-White-Matter": ["Left-Cerebral-Cortex", "Right-Cerebral-Cortex"]
   }


.. toctree::
   :maxdepth: 2
   :caption: Contents:
