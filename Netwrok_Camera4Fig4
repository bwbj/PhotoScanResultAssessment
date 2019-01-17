# -*- coding: utf-8 -*-
"""
Created on Sat Aug 25 19:42:10 2018
Teng Li modified from Alexey Pasumansky and tkwasnitschka in agisoft Forum
see: http://www.agisoft.com/forum/index.php?topic=1412.0 
and: http://www.agisoft.com/forum/index.php?topic=6989 for detail.
20190115note: why not made some meanful comments half year ago?
"""
import PhotoScan

doc = PhotoScan.app.document
chunk = doc.chunk
point_cloud = chunk.point_cloud
points = point_cloud.points
point_proj = point_cloud.projections
npoints = len(points)

photo_matches = dict()

path = PhotoScan.app.getSaveFileName("Specify export path and filename:")
print("exporting to", path)
file = open(path, "wt")
# file.write("Image_A Coords_A Image_B Coords_B\n")

step = PhotoScan.app.getInt("Minimum number of valid tie points:" ,3)

for photo in chunk.cameras:
	
    total = set() #only valid
    point_index = 0
    proj = point_proj[photo]
	
    for cur_point in proj:
				
        track_id = cur_point.track_id
        while point_index < npoints and points[point_index].track_id < track_id:
            point_index += 1
        if point_index < npoints and points[point_index].track_id == track_id:
			
            if point_cloud.points[point_index].valid:
                total.add(point_index)
#	print("listing photo:", photo.label)		
    photo_matches[photo] = total
	
for i in range(0, len(chunk.cameras) - 1):
    if chunk.cameras[i] not in photo_matches.keys():
        continue
		
    for j in range(i + 1, len(chunk.cameras)):
	
        if chunk.cameras[j] not in photo_matches.keys():
            continue

        matches = photo_matches[chunk.cameras[i]] & photo_matches[chunk.cameras[j]]
        if len(matches) > step:
            pos_i = chunk.crs.project(chunk.transform.matrix.mulp(chunk.cameras[i].center))
            pos_j = chunk.crs.project(chunk.transform.matrix.mulp(chunk.cameras[j].center))
#        file.write('{0} {1} {2} {3} {4}\n'.format(chunk.cameras[i].label, pos_i, chunk.cameras[j].label, pos_j, len(matches)))
            file.write("%s %s %s %s %d\n" %(chunk.cameras[i].label, pos_i, chunk.cameras[j].label, pos_j, len(matches)))
#			print("matching:",chunk.cameras[i].label," & ", chunk.cameras[j].label)
        else:
            continue
print("export of valid matches completed!")
file.close()
