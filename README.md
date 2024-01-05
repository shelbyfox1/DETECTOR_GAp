// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Asch-

//@version=4 
study(title="GAP DETECTOR", overlay=true, max_bars_back = 3000)

length = input(1500, minval=1, maxval=3000)
width = input(2, minval=1, maxval=5, options = [1,2,3,4,5])

gap_start = high[length]
gap_end = low[length - 1]
gap_bull = false
gap_bear = false
inf_gap = 0.0
sup_gap = 0.0

if barstate.islast
    for j = 1 to length
        gap_bull := false
        gap_bear = false
        inf_gap := 0.0
        sup_gap := 0.0
        
        if high[j] < low[j - 1] //bull gap
            gap_start := high[j]
            gap_end := low[j - 1]
            gap_bull := true
            inf_gap := gap_start
            sup_gap := gap_end
            for i = (j-1) to 0
                sup_gap := min(sup_gap, low[i])
            if ((sup_gap - inf_gap)/(syminfo.mintick*10) < 5) // only gap > 5 pips are considered
                gap_bull := false
                
        if low[j] > high[j - 1] //bear gap
            gap_start := low[j]
            gap_end := high[j - 1]
            gap_bear := true
            inf_gap := gap_end
            sup_gap := gap_start
            for i = (j-1) to 0
                inf_gap := max(inf_gap, high[i])
            if ((sup_gap - inf_gap)/(syminfo.mintick*10) < 5) // only gap > 5 pips are considered
                gap_bear := false
        
        if gap_bull
            label_bull = label.new(bar_index[j], na, 'Bull gap: ' + tostring((sup_gap - inf_gap)/(syminfo.mintick*10)) + ' pips\n' + '[' + tostring(inf_gap) + ' ; ' + tostring(sup_gap) + ']',
              color=color.green,
              textcolor=color.white,
              style=label.style_labelup, yloc=yloc.belowbar)
            //label.delete(label_bull[1])
        
            line_bull_inf = line.new(x1 = bar_index[j], y1 = inf_gap, 
                                     x2 = bar_index, y2 = inf_gap,
                                     extend=extend.right, color = color.green, style = line.style_solid, width = width)
            //line.delete(line_bull_inf[1])
            
            line_bull_sup = line.new(x1 = bar_index[j-1], y1 = sup_gap, 
                                     x2 = bar_index, y2 = sup_gap,
                                     extend=extend.right, color = color.green, style = line.style_solid, width = width)
            //line.delete(line_bull_sup[1])
            
        
        if gap_bear
            label_bear = label.new(bar_index[j], na, 'Bear gap: ' + tostring((sup_gap - inf_gap)/(syminfo.mintick*10)) + ' pips\n' + '[' + tostring(inf_gap) + ' ; ' + tostring(sup_gap) + ']',
              color=color.red,
              textcolor=color.white,
              style=label.style_labeldown, yloc=yloc.abovebar)
            //label.delete(label_bear[1])
        
            line_bear_inf = line.new(x1 = bar_index[j-1], y1 = inf_gap, 
                                     x2 = bar_index, y2 = inf_gap,
                                     extend=extend.right, color = color.red, style = line.style_solid, width = width)
            //line.delete(line_bear_inf[1])
            
            line_bear_sup = line.new(x1 = bar_index[j], y1 = sup_gap, 
                                     x2 = bar_index, y2 = sup_gap,
                                     extend=extend.right, color = color.red, style = line.style_solid, width = width)
            //line.delete(line_bear_sup[1])
            
