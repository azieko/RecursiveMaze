# RecursiveMaze 
"""Maze generation and path finding"""

__author__ = "Azieko"

from random import sample




class Cell:
    """
    Cell objects represent a single maze location with up-to 4 walls.

    The .N, .E, .S, .W attributes represent the walls in the North,
    East, South and West directions. If the attribute is True, there is a
    wall in the given direction.

    The .x and .y attributes store the coordinates of the cell.
    """

    def __init__(self, x, y):
        self.x = x
        self.y = y

        self.N = True
        self.E = True
        self.S = True
        self.W = True 

    def remove_wall(self, direction):
        """
        Remove one wall - keep all neighbors consistent
        Direction is one of these strings: 'N', 'E', 'S', 'W'
        """
        direction = direction.upper()

        loc = " @(x=%d, y=%d)" % (self.x, self.y)
        if direction == "W":
            if self.x < 1:
                raise ValueError("cannot remove side wall on west" + loc)
            if self.W:
                self.W = False
                assert maze[self.x - 1][self.y].E
                maze[self.x - 1][self.y].E = False
        if direction == "E":
            if self.x >= size_x - 1:
                raise ValueError("cannot remove side wall on east" + loc)
            if self.E:
                self.E = False
                assert maze[self.x + 1][self.y].W
                maze[self.x + 1][self.y].W = False
        if direction == "N":
            if self.y < 1:
                raise ValueError("cannot remove side wall on north" + loc)
            if self.N:
                self.N = False
                assert maze[self.x][self.y - 1].S
                maze[self.x][self.y - 1].S = False
        if direction == "S":
            if self.y >= size_y - 1:
                raise ValueError("cannot remove side wall on south" + loc)
            if self.S:
                self.S = False
                assert maze[self.x][self.y + 1].N
                maze[self.x][self.y + 1].N = False

    def has_wall(self, direction):
        """
        True if there is a wall in the given direction
        Direction is one of these strings: 'N', 'E', 'S', 'W'
        """
        return getattr(self, direction.upper())


# Global variables for the maze and its size
size_x = size_y = 32
maze = [[Cell(x, y) for y in range(size_y)] for x in range(size_x)]



def build_maze_recursive(x,y,visited):
    directions = [ 
        ("N", x, y-1),
        ("S", x, y+1),
        ("E", x+1, y),
        ("W", x-1, y)
        ]  

 
    visited.add((x,y)) 
    
    #outside = [(new_x,0),(size_x,new_y),(new_x,size_y),(0,new_y)]
                
    for d, new_x, new_y in sample(directions,len(directions)):
        if 0<= new_x < size_x and 0<= new_y < size_y: 
            #if maze[new_x][new_y].has_wall(d): 
            if (new_x,new_y) not in visited:
                #visited.add((new_x,new_y)) 
                maze[x][y].remove_wall(d) 
                build_maze_recursive(new_x,new_y,visited) 


                    
  
    
    
     
            
    
def build_maze():
    """
    Build a valid maze by tearing down walls

    The function has access to the following global variables:
        size_x - integer, the horizontal size of the maze
        size_y - integer, the vertical size of the maze
        maze - a two dimensional array (list of lists) for all cells
            e.g. maze[3][4] is a Cell object for x=3, y=4

    This function does not need to return any value but should modify the
    cells (walls) to create a perfect maze.
    When the function is invoked all cells have all their four walls standing.
    """   
    visited = set()
    build_maze_recursive(0, 0,visited) 
    



def find_path_recursive(x,y,path,end):
    if path[-1] == end:
        return path 

    directions = [ 
        ("N", x, y-1),
        ("S", x, y+1),
        ("E", x+1, y),
        ("W", x-1, y)
        ]  
    
                
    for d, new_x, new_y in directions:
        if 0<= new_x < size_x and 0<= new_y < size_y: 
            if (new_x,new_y) not in path:
                if not maze[x][y].has_wall(d):
                    #if (new_x,new_y) == end: 
                        #path.append((new_x,new_y))
                        #return path 
                    #path.append((new_x,new_y))
                    e = find_path_recursive(new_x,new_y,path+[(new_x,new_y)],end)
                    if e:
                        return e
        


    
    

def find_path(start, end):
    """
    Find a path from the start position to the end

    The start and end parameters are coordinate pairs (tuples) for the
    start and end (target) position. E.g. (0, 0) or (7, 13).

    The function has access to the following global variables:
        size_x - integer, the horizontal size of the maze
        size_y - integer, the vertical size of the maze
        maze - a two dimensional array (list of lists) for all cells
            e.g. maze[3][4] is a Cell object for x=3, y=4

    The function is invoked after build_maze removed the walls to create a
    perfect maze.

    This function shall return a list of coordinate pairs (tuples or lists)
    which list the cell coordinates on a valid path from start to end.
    E.g.: [(0, 0), (0, 1), (1, 1), (2, 1), (3, 1), ..., (7, 13)]
    """ 
    path = [start]
    final = find_path_recursive(0,0,path,end)
    return final 
    
    

    


###############################################################################
# Testing and visualizing results - no need to understand and/or change
def draw_maze(start=None, end=None, path=None):
    """Draw the maze and the path in a graphical window"""
    import tkinter
    cell_size = 20
    master = tkinter.Tk()
    canvas = tkinter.Canvas(master, width=size_x * cell_size + 1,
                            height=size_y * cell_size + 1,
                            bd=0, highlightthickness=0, relief='ridge')
    canvas.pack()
    for x in range(size_x):
        for y in range(size_y):
            if maze[x][y].N:
                canvas.create_line(cell_size * x, cell_size * y,
                                   cell_size * (x + 1), cell_size * y)
            if maze[x][y].E:
                canvas.create_line(cell_size * (x + 1), cell_size * y,
                                   cell_size * (x + 1), cell_size * (y + 1))
            if maze[x][y].S:
                canvas.create_line(cell_size * x, cell_size * (y + 1),
                                   cell_size * (x + 1), cell_size * (y + 1))
            if maze[x][y].W:
                canvas.create_line(cell_size * x, cell_size * y,
                                   cell_size * x, cell_size * (y + 1))

    if path is not None:
        line = [x * cell_size + cell_size // 2 for step in path for x in step]
        canvas.create_line(*line, fill='red', width=2)

    radius = cell_size // 3
    if start is not None:
        img_start = [cell_size * x + cell_size // 2 for x in start]
        canvas.create_oval(img_start[0] - radius,
                           img_start[1] - radius,
                           img_start[0] + radius,
                           img_start[1] + radius, fill='red')
    if end is not None:
        img_end = [cell_size * x + cell_size // 2 for x in end]
        canvas.create_oval(img_end[0] - radius,
                           img_end[1] - radius,
                           img_end[0] + radius,
                           img_end[1] + radius, fill='green')

    master.title('Maze')
    master.lift()
    master.call('wm', 'attributes', '.', '-topmost', True)
    tkinter.mainloop()


def main():
    import sys

    sys.setrecursionlimit(4096)

    print("Testing build_maze()...")
    build_maze()

    # checking maze
    maze_ok = True
    n_edges = 0
    for x in range(size_x):
        for y in range(size_y):
            n_node_edges = 0
            for direction in 'NESW':
                n_node_edges += not maze[x][y].has_wall(direction)
            if n_node_edges < 1:
                print('ERROR: walled in cell @ (x=%d, y=%d)' % (x, y))
                maze_ok = False
            n_edges += n_node_edges
    n_perfect_edges = (size_x * size_y - 1) * 2
    if n_edges < n_perfect_edges:
        print('ERROR: not a perfect maze, too many walls')
        maze_ok = False
    if n_edges > n_perfect_edges:
        print('ERROR: not a perfect maze, redundant paths')
        maze_ok = False

    if not maze_ok:
        print("Error in maze building task (fix this first): 0 pts")
        draw_maze()
        return

    print("Testing find_path()...")
    start, end = (0, 0), (size_x - 1, size_y - 1)
    path = find_path(start, end)

    # checking path
    path_ok = True
    try:
        assert len(path) >= 2
        if path[0] != start:
            print('ERROR: invalid starting point for path', path[0])
            path_ok = False
        if path[-1] != end:
            print('ERROR: invalid endpoint for path', path[-1])
            path_ok = False

        prev = None
        for step in path:
            assert 0 <= step[0] < size_x
            assert 0 <= step[1] < size_y
            if prev is not None:
                dst = abs(step[0] - prev[0]) + abs(step[1] - prev[1])
                if dst != 1:
                    print('ERROR: invalid step in path', prev, step)
                    path_ok = False
            prev = step

    except Exception as e:
        print(e)
        print('ERROR: invalid path object:', path)
        path_ok = False
        path = None

    if path_ok:
        print("Maze and path looks good: 100 pts")
    else:
        print("Maze looks good, but incorrect path: 50 pts")

    draw_maze(start, end, path)


if __name__ == '__main__':
    main() 
