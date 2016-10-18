"""
    Author: Paul Poinet
    Date : 18.10.2016
    
    Inputs: 
        L (list of lines)
        T (tolerance value)
    Outputs:
        P : Ordered list of points.
        LP : For each line lists both end points indices.
        PP : For each point lists all point indices connected to it.
        PL : For each point lists all lines connected to it.
"""

# Import Library
import Rhino.Geometry as rg

# Name Component
ghenv.Component.Name = "Line_Topologizer"
ghenv.Component.NickName = 'LineTopo'


def pythonListTGhDataTree(pythonList, listType):
    
    """ Converts a nested Python list to a GH datatree. """
    
    import Grasshopper as gh
    
    dataTree = gh.DataTree[listType]()
    for i in range(len(pythonList)):
        for j in pythonList[i]:
            dataTree.Add(j,gh.Kernel.Data.GH_Path(i))
            
    return dataTree

def PtCloudFromPoints(Points, T):
    
    """ Creates a PointCloud from points. Removes duplicated input points. """
    
    PtCloud = rg.PointCloud()
    for pt in Points:
        if len(PtCloud.GetPoints()) == 0:
            PtCloud.Add(pt)
        Dis = rg.Point3d.DistanceTo(PtCloud.GetPoints()[PtCloud.ClosestPoint(pt)], pt)
        if len(PtCloud.GetPoints()) > 0 and Dis > T:
            PtCloud.Add(pt)
    
    return PtCloud

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
        
        return PtCloudFromPoints(PolyPts, T).GetPoints()
    
    
    
    def LineToPoints_Structure(self):
        
        """ For each line lists both end points indices. """
        
        LineToPoints = []
        for line in L:
            
            indexStart = rg.PointCloud(self.OrderedListOfPoints()).ClosestPoint(line.From)
            indexEnd = rg.PointCloud(self.OrderedListOfPoints()).ClosestPoint(line.To)
            LineToPoints.append([indexStart, indexEnd])
            
        return LineToPoints
        
    
    def PointToPoints_Structure(self):
        
        """ For each point lists all point indices connected to it. """
        
        PointToPoints = []
        for i in range(len(self.OrderedListOfPoints())):
            coTuples = []
            for j in range(len(self.LineToPoints_Structure())):
                if i in self.LineToPoints_Structure()[j]:
                    coTuples.append(j)
            coTupleIndices = []
            for k in coTuples:
                coTupleIndices.append(self.LineToPoints_Structure()[k])
                
            coPointsIndices = []
            for cPts in coTupleIndices:
                if cPts[0] != i:
                    coPointsIndices.append(cPts[0])
                if cPts[1] != i:
                    coPointsIndices.append(cPts[1])
                    
            PointToPoints.append(coPointsIndices)
            
        return PointToPoints
        

    def PointToLines_Structure(self):
        
        """ For each point lists all lines connected to it. """
        
        PointToLines = []
        
        for i in range(len(self.OrderedListOfPoints())):
            coTuples = []
            for j in range(len(self.LineToPoints_Structure())):
                if i in self.LineToPoints_Structure()[j]:
                    coTuples.append(j)
            coLines = []
            for k in coTuples:
                coLines.append(L[k])
            PointToLines.append(coLines)
        return PointToLines


# Outputs

P  = LineTopologizer(L, T).OrderedListOfPoints()

LP = LineTopologizer(L, T).LineToPoints_Structure()
LP_gh = pythonListTGhDataTree(LP,int) # Grasshopper datatree output

PP = LineTopologizer(L, T).PointToPoints_Structure()
PP_gh = pythonListTGhDataTree(PP,int) # Grasshopper datatree output

PL = LineTopologizer(L, T).PointToLines_Structure()
PL_gh = pythonListTGhDataTree(PL, rg.Line) # Grasshopper datatree output
