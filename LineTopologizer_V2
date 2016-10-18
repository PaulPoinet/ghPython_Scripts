"""
    Cleans a network of lines and generates basic topological information.
    
    Inputs: 
        L: Network of Lines {list, line}
        T: Tolerance value {item, float}
    Outputs:
        P: Ordered list of points {list, point}
        LP_gh: For each line lists both end points indices {tree, int}
    Remarks:
        Author: Paul Poinet
        Version: 161018
"""

# Import Library
import Rhino.Geometry as rg
import time

# Name Component
ghenv.Component.Name = "LineTopologizer"
ghenv.Component.NickName = 'LineTopo'

# Global Definition ----------------------------------------------------------------------------

def PythonListTGhDataTree(pythonList, listType):
    """ Converts a nested Python list to a GH datatree. """
    import Grasshopper as gh
    dataTree = gh.DataTree[listType]()
    for i in range(len(pythonList)):
        for j in pythonList[i]:
            dataTree.Add(j,gh.Kernel.Data.GH_Path(i))
    return dataTree

# Main class ------------------------------------------------------------------------------------

class LineTopologizer:
    
    def __init__(self, L, T):
        """ Global variables. """
        self.L = L
        self.T = T
        
    def OrderedListOfPoints(self):
        """ Ordered list of points. """
        PolyPts = []
        for line in L:
            PolyPts.append(line.From)
            PolyPts.append(line.To)
        return rg.Point3d.CullDuplicates(PolyPts, T)
        
    def LineToPoints_Structure(self):
        """ For each line lists both end points indices. """
        LineToPoints = []
        PtCloud = rg.PointCloud(self.OrderedListOfPoints())
        for line in L:
            indexStart = PtCloud.ClosestPoint(line.From)
            indexEnd = PtCloud.ClosestPoint(line.To)
            LineToPoints.append([indexStart, indexEnd])
        return LineToPoints

# Outputs ----------------------------------------------------------------------------------------

P  = LineTopologizer(L, T).OrderedListOfPoints()

LP = LineTopologizer(L, T).LineToPoints_Structure()
LP_gh = PythonListTGhDataTree(LP,int) # Grasshopper datatree output
