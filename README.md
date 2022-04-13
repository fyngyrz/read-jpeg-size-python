# read-jpeg-size-python
Quickly and efficiently read jpeg size for python 2 and 3

# Python 2

```python2
# Python 2
# reads jpeg image size quickly and efficiently
# returns width,height or 0,0 if cannot determine
def jpeg_res(filename):
	try:	# catch every type of exception
		too = True
		ct = 0
		# open image for reading in binary mode
		with open(filename,'rb') as img:
			x = img.read(2)	# Look for jpeg file type marker
			if ord(x[0]) != 0xff or ord(x[1]) != 0xd8:
				return 0,0	# not a jpeg
			while too:
				ct = ct + 1
				if ct > 100:	# frame block should be pretty early... quit if not
					return 0,0
				x = img.read(2)	# read marker
				l = img.read(2)	# read size (includes size bytes... bizarre, but true)
				if ord(x[0]) == 0xff and ord(x[1]) == 0xc0:
					ll = (ord(l[0]) * 256) + ord(l[1])	# get block size
					z = img.read(ll-2)					# read block, minus size bytes
					height = ord(z[1]) * 256 + ord(z[2])
					width  = ord(z[3]) * 256 + ord(z[4])
					return width,height
				else: # not the right kind of block
					ll = (ord(l[0]) * 256) + ord(l[1])	# get block size
					img.seek(ll-2,1)					# seek relative past the block
	except Exception,e: # you can use str(e) to figure out the problem here
		pass	# something went completely wrong.
	return 0,0	# error return

```
# Python 3:
```python3
# Python 3
# Read jpeg image size quickly and efficiently
# returns width, height or 0,0 if cannot determine
def jpeg_res(filename):
	try:
		too = True
		ct = 0
		# open image for reading in binary mode
		with open(filename,'rb') as img:
			x = img.read(2) # JPEG marker
			if x[0] != 0xff or x[1] != 0xd8:
				return 0,0	# not a jpeg
			while too:
				ct = ct + 1
				if ct > 100:	# frame block should be pretty early... quit if not
					return 0,0
				x = img.read(2)	# read marker
				l = img.read(2)	# read size
				if x[0] == 0xff and x[1] == 0xc0:
					ll = l[0] * 256 + l[1]	# get block size
					z = img.read(ll)		# read block
					height = z[1] * 256 + z[2]
					width  = z[3] * 256 + z[4]
					return width,height
				else: # not the right kind of block
					ll = l[0] * 256 + l[1]	# get block size
					img.seek(ll-2,1)	# seek past the block
	except Exception as e:	# can diag errors with str(e)
		print(str(e))
		return 0,0

```
