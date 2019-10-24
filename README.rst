imdirect
========

|Build Status| |Coverage Status|

PIL extension performing automatic rotation of opened JPEG images.

**imdirect has been archived due to PIL having a built in functionality to perform the same tasks. If you're using Pillow >= 6.0.0, you can use the built-in ImageOps.exif_transpose function do correctly rotate an image according to its exif tag:**

.. code:: python

    from PIL import ImageOps
    image = ImageOps.exif_transpose(image)
    

Description
-----------

The orientation of the photographed object or scene with respect to the
digital camera is encoded in the resulting image's Exif [1]_ data
(given that it is saved as a JPEG). When working with such digital
camera images, this orientation might lead to problems handling the
image and is very often desired to be counteracted.

This module is a small extension to `Pillow <https://pillow.readthedocs.io/en/3.3.x/>`_ that
`monkey patches <https://en.wikipedia.org/wiki/Monkey_patch>`_
the `PIL.Image.open <http://pillow.readthedocs.io/en/3.3.x/reference/Image.html#PIL.Image.open>`_ method
to automatically rotate the image [2]_ (by lossless methods) and update
the Exif tag accordingly, given that image is a JPEG.

The package also features a save method that includes the Exif data
by default when saving JPEGs.

Installation
------------

::

    pip install imdirect

Usage
-----

Demonstration of the monkey patching and how it works:

.. code:: python

   >>> from PIL import Image
   >>> import imdirect
   >>> img = Image.open('image.jpg')
   >>> print("{0}, Orientation: {1}".format(img, img._getexif().get(274)))
   <PIL.JpegImagePlugin.JpegImageFile image mode=RGB size=4032x3024 at 0x7F44B5E4FF10>, Orientation: 6
   >>> imdirect.monkey_patch()
   >>> img_autorotated = Image.open('image.jpg')
   >>> print("{0}, Orientation: {1}".format(img_autorotated, img_autorotated._getexif().get(274)))
   <PIL.JpegImagePlugin.JpegImageFile image mode=RGB size=3024x4032 at 0x7F44B5DF5150>, Orientation: 1


The package can also be used without monkey patching, by applying the
``imdirect.imdirect_open`` method directly:

.. code:: python

   >>> from imdirect import imdirect_open
   >>> img = imdirect_open('image.jpg')

or by using the ``imdirect.autorotate`` on a ``PIL.Image.Image`` object:

.. code:: python

   >>> from PIL import Image
   >>> import imdirect
   >>> img = Image.open('image.jpg')
   >>> img_rotated = imdirect.autorotate(img)

The last method does not return a ``PIL.JpegImagePlugin.JpegImageFile``, but can still be used
if the Exif information of the original image is undesired.

Tests
~~~~~

Tests can be run with `pytest <http://doc.pytest.org/en/latest/>`_:

.. code:: sh

   Testing started at 13:28 ...
   ============================= test session starts ==============================
   platform linux2 -- Python 2.7.12, pytest-3.0.1, py-1.4.31, pluggy-0.3.1
   rootdir: /home/hbldh/Repos/imdirect, inifile:
   collected 4 items

   test_autorotate.py ...
   test_monkey_patching.py .

   =========================== 4 passed in 0.08 seconds ===========================

References
----------

.. [1] Exif on Wikipedia (https://en.wikipedia.org/wiki/Exif)

.. [2] Exif orientation (http://sylvana.net/jpegcrop/exif_orientation.html)


.. |Build Status| image:: https://travis-ci.org/hbldh/imdirect.svg?branch=master
   :target: https://travis-ci.org/hbldh/imdirect
.. |Coverage Status| image:: https://coveralls.io/repos/github/hbldh/imdirect/badge.svg?branch=master
   :target: https://coveralls.io/github/hbldh/imdirect?branch=master
