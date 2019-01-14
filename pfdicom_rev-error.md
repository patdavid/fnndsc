# pfdicom_rev error

Following the instructions on http://fnndsc.childrens.harvard.edu/devchallenge/pages/4-tojpeg/tojpeg.html trips an error at some point that stops script execution.
It appears when attempting to work on `/agesort/./03-yr/04-mo/subj-3-14482-ex/ep_moco_nav_set-WIPmocoMEMPRAGE_FOV_192-235125/dcm2jpgResize` and trips and error about the file existing already.

I haven't had a chance to look at the pfdicom_rev.py to see if there could be some error handling included.


## The Error

```
2019-01-14 09:27:49  |          mother |      pfdicom_rev.py:pfdicom_rev.jpegs_generateFromDCM() | Generating jpgs from dcm... generated 137 jpgs.
2019-01-14 09:27:53  |          mother |               pfdicom_rev.py:pfdicom_rev.jpegs_resize() | Resizing jpgs for mosiac... Traceback (most recent call last):
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/bin/pfdicom_rev", line 446, in <module>
    description         = str_desc
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/site-packages/pfdicom_rev/pfdicom_rev.py", line 1198, in run
    d_process   = func_process()
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/site-packages/pfdicom_rev/pfdicom_rev.py", line 1110, in processDCM
    persistAnalysisResults  = False
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/site-packages/pftree/pftree.py", line 662, in tree_process
    loop_nonThreaded()
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/site-packages/pftree/pftree.py", line 556, in loop_nonThreaded
    if fn_outputWriteCallback:  dret_outputSet  = outputSet_write(path, d_tree[path])
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/site-packages/pftree/pftree.py", line 521, in outputSet_write
    ( '%s/%s' % (self.str_outputDir, path), data), **kwargs
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/site-packages/pfdicom_rev/pfdicom_rev.py", line 640, in outputSaveCallback
    jpegs_resize()
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/site-packages/pfdicom_rev/pfdicom_rev.py", line 482, in jpegs_resize
    '%s/%s' % (path, self.str_dcm2jpgDirResize)
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/shutil.py", line 315, in copytree
    os.makedirs(dst)
  File "/home/pat/Documents/BostonChildrensDev/fnndsc/lib/python3.6/os.py", line 220, in makedirs
    mkdir(name, mode)
FileExistsError: [Errno 17] File exists: '/home/pat/Documents/BostonChildrensDev/fnndsc-project/agesort/./03-yr/04-mo/subj-3-14482-ex/ep_moco_nav_set-WIPmocoMEMPRAGE_FOV_192-235125/dcm2jpgResize'
(fnndsc)
```
