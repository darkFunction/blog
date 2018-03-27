---
title: "French verbs crossword"
date: 2018-03-27
tags: [ "python", "flask", "js" ]
---

Added a [crossword section](http://verbedujour.com/crossword) to VerbeDuJour for practicing the memorisation of infinitives.

![Crossword preview](/images/crossword.png)

Below is the Typescript utility which takes an array of answers and compresses them into a crossword table with as many crossover points as possible. This allows the site to dynamically generate crosswords every month from daily verbs.


```js	
function createTable(tableData) {
  var table = document.createElement('table');
  var tableBody = document.createElement('tbody');

  tableData.forEach(function(rowData) {
    var row = document.createElement('tr');

    rowData.forEach(function(cellData) {
      var cell = document.createElement('td');
      if (cellData) {
          cell.appendChild(document.createTextNode(cellData));
        }
    
          row.appendChild(cell);
    });

    tableBody.appendChild(row);
  });

  table.appendChild(tableBody);
  document.body.appendChild(table);
}

function sortStringsByLength(stringArray: string[]) {
    return stringArray.sort((a, b) => {
        if (a.length > b.length) {
            return -1;
        }
        if (b.length > a.length) {
            return 1;
        }
        return 0;
    }); 
}

type Coord = [number, number]

// Represents a single entry in the crossword
class Bar { 
    word: string;
    position: Coord;
    isDown: boolean;

    constructor(word: string, position: Coord, isDown: boolean) {
        this.word = word
        this.position = position
        this.isDown = isDown
    }
}

class Rect {
    x: number;
    y: number;
    w: number;
    h: number;
    
    constructor(x: number, y: number, w: number, h: number) {
        this.x = x; this.y = y; this.w = w; this.h = h;
    }
}

class Crossword {

    pool:Bar[] = []     // Unplaced words
    actives:Bar[] = []  // Placed words

    constructor(answers: string[]) { 

        // Create Bars ordered by wordlength and add to pending pool
        var sortedAnswers = sortStringsByLength(answers);
        for (var answer of sortedAnswers) {
            var bar = new Bar(answer.toUpperCase(), null, false);
            this.pool.push(bar)
        }
    
        // Add longest word to crossword at (0, 0) immediately
        var firstBar:Bar = this.pool[0]
        firstBar.position = [0, 0]
        this.actives.push(firstBar)

        this.build()
    }

    build() {
        
        for (var bar of this.pool) {
            this.addBarToActives(bar)   
        }   

        // Tidy up stragglers that only have one connection     
        for (var pass=0; pass<5; pass++) {          
            for (var bar of this.actives) {
                var score = this.barScore(bar)
                if (score == 1) {
                    var index = this.actives.indexOf(bar) 
                    this.actives.splice(index, 1)
                    this.addBarToActives(bar)
                }
            }
        }
        //createTable(this.buildGrid())
    }

    barScore(bar: Bar): number {
        var score = 0
        for (var activeBar of this.actives) {   
            if (activeBar == bar) {
                continue
            }
            var collisionPoint = this.collisionPoint(bar, activeBar) 
        
            if (collisionPoint != null) {
                // If different axis    
                if (activeBar.isDown != bar.isDown) {  
                    if (bar.word[collisionPoint[0]] == activeBar.word[collisionPoint[1]]) {
                        // Same letter  
                        score = score + 1
                    } else {
                        // Letter conflict, not valid
                        return 0
                    }
                } else { 
                    // Same axis and collide, not valid
                    return 0
                }
            } else {

                // Test a 'neighbour' collision, ie, don't be right next to another word
                var newRect = this.rectForBar(bar)
                var activeRect = this.rectForBar(activeBar)
                
                // Make two sub-rects which pad activeRect by one on each side but cut off the corners
                var activeRectWide = new Rect(activeRect.x - 1, activeRect.y, activeRect.w + 2, activeRect.h)
                var activeRectHigh = new Rect(activeRect.x, activeRect.y - 1, activeRect.w, activeRect.h + 2)
            
                if (this.rectCollides(activeRectWide, newRect) || this.rectCollides(activeRectHigh, newRect)) {
                    return 0    
                } 
            }
        }
        return score
    }

    addBarToActives(newBar: Bar) {
        if (this.actives.indexOf(newBar) > -1) {
            return
        }   
        var positions = this.positionsForWord(newBar.word)  
        var bestPosition = null 
        var highestScore = 0
        for (var pos of positions) {
            newBar.position = [pos[1], pos[2]]
            newBar.isDown = pos[0]
            // Score each position
            var score = this.barScore(newBar)
        
            if (score > 0) {
                if (score > highestScore) {
                    highestScore = score
                    bestPosition = pos
                } else if (score == highestScore) {
                    // Decide between positions based on smallest extreme
                    newBar.position = [pos[1], pos[2]]
                    newBar.isDown = pos[0]
                    this.actives.push(newBar)
                    var newBoardRect = this.boardRect()
                
                    newBar.position = [bestPosition[1], bestPosition[2]]
                    newBar.isDown = bestPosition[0]
                    var currentBoardRect = this.boardRect()

                    this.actives.splice(this.actives.indexOf(newBar), 1); 
                        
                    var wDiff = newBoardRect.w - currentBoardRect.w
                    var hDiff = newBoardRect.h - currentBoardRect.h
                    var diff = Math.abs(wDiff) > Math.abs(hDiff) ? wDiff : hDiff    
                    if (diff < 0) {
                        bestPosition = pos
                    }
                }
            }
        }               
        if (bestPosition != null) {
            newBar.position = [bestPosition[1], bestPosition[2]]
            newBar.isDown = bestPosition[0]
            this.actives.push(newBar)
        } else {
            //console.log("Could not place word: " + newBar.word)   
        }
    }

    getActives() {
        var boardRect = this.boardRect()
        this.normaliseActives(boardRect)
        return this.actives
    }

    buildGrid() {
        var boardRect = this.boardRect()
        this.normaliseActives(boardRect)
        var grid = [[]]
        for (var y=0; y<boardRect.h; y++) {
            grid[y] = []
            for (var x=0; x<boardRect.w; x++) {
                grid[y][x] = null 
            }
        }
        for (var activeBar of this.actives) {   
            for (var i=0; i<activeBar.word.length; i++) {
                var x = activeBar.position[0]
                var y = activeBar.position[1]
                if (activeBar.isDown) {
                    grid[y + i][x] = activeBar.word[i]
                } else {
                    grid[y][x + i] = activeBar.word[i]
                }
            }
        }
        return grid
    }

    boardRect(): Rect {
        var minX = 0
        var minY = 0
        var maxX = 0
        var maxY = 0
        for (var activeBar of this.actives) {   
            var x = activeBar.position[0]
            var y = activeBar.position[1]
            var len = activeBar.word.length
            if (x < minX)
                minX = x    
            if (y < minY)
                minY = y    
            
            if (activeBar.isDown) 
                y += len
            else
                x += len

            if (x > maxX) 
                maxX = x
            if (y > maxY) 
                maxY = y
        }

        return new Rect(minX, minY, maxX - minX, maxY - minY)
    }

    normaliseActives(boardRect: Rect) {
        for (var activeBar of this.actives) {   
            activeBar.position[0] -= boardRect.x
            activeBar.position[1] -= boardRect.y
        }
    }

    rectCollides(a: Rect, b: Rect) : boolean {
        if (a.x + a.w <= b.x) return false
        if (a.x >= b.x + b.w) return false
        if (a.y + a.h <= b.y) return false
        if (a.y >= b.y + b.h) return false
        return true
    }
        
    rectForBar(bar: Bar): Rect {
        var w = bar.isDown ? 1 : bar.word.length
        var h = bar.isDown ? bar.word.length : 1
        return new Rect(bar.position[0], bar.position[1], w, h)
    }

    // Find letter indices which are the points of intersection between two bars    
    collisionPoint(bar1: Bar, bar2: Bar) : Coord {
        if (! this.rectCollides(this.rectForBar(bar1), this.rectForBar(bar2))) 
            return null

        var hBar = bar1.isDown ? bar2 : bar1
        var vBar = bar1.isDown ? bar1 : bar2    
        
        var vPoint = hBar.position[1] - vBar.position[1]
        var hPoint = vBar.position[0] - hBar.position[0]

        return bar1 == hBar ? [hPoint, vPoint] : [vPoint, hPoint]
    }

    // Find viable positions on every active bar
    positionsForWord(word: string) {
        var positions = []
        for (var activeBar of this.actives) {   
            var crosspoints = this.crosspoints(word, activeBar.word)
            var isDown = !activeBar.isDown
            var anchor = activeBar.position
            for (var xp of crosspoints) {
                var x = isDown ? anchor[0] + xp[1] : anchor[0] - xp[0] 
                var y = isDown ? anchor[1] - xp[0] : anchor[1] + xp[1]
                var pos = [isDown, x, y]    
                positions.push(pos)
            }               
        }
        return positions
    }   

    // Find letter indices where strings cross
    crosspoints(str1: string, str2:string) {
        var crosspoints = []

        for (var i=0; i<str1.length; i++) {
            var c1 = str1[i]
            for (var j=0; j<str2.length; j++) {
                var c2 = str2[j]
                if (c1 == c2) {
                    crosspoints.push([i, j])    
                }                   
            }
        }   
        return crosspoints
    }
}
```
