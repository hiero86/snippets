import { Button } from '@salesforce/design-system-react';
import { GridApi } from 'ag-grid-community';
 
import { FilterX, Fullscreen } from 'lucide-react';
 
const ToolbarOptions = ({ gridApi }: { gridApi?: GridApi }) => {
  return (
    <div className="flex items-center gap-3 me-2">
      <Button
        onClick={() => gridApi?.refreshServerSide({ purge: true })}
        iconCategory="utility"
        iconName="refresh"
        iconVariant="bare"
        variant="icon"
        title="Refresh"
      />
 
      <Button
        onClick={() => gridApi?.setFilterModel(null)}
        iconVariant="bare"
        variant="icon"
        title="Clear Filters"
      >
        <FilterX size={18} />
      </Button>
 
      <Button
        onClick={() => gridApi?.sizeColumnsToFit()}
        iconVariant="bare"
        variant="icon"
        title="Size Columns To Fit"
      >
        <Fullscreen size={18} />
      </Button>
    </div>
  );
};
 
export default ToolbarOptions;
